+++
title = "开发实践: Golang 在生成大文件MD5超时控制"
draft = false

[taxonomies]
tags = ["golang", "md5", "开发实践"]
categories = [""]
+++

## 问题背景

工作中遇到一个问题：在当前项目的程序运行中，需要对MySQL实例的备份集文件生成MD5信息。
在少数情况下备份集文件可能会非常大，MD5信息生成会非常慢。从需求上看，希望能对该过程进行一个超时控制：当超过30分钟后，不
再生成MD5信息，直接向上返回结果。


样例代码
```go
func GetFileMd5(filePath string) (string, error) {
	file, err := os.Open(filePath)
	if nil != err {
		return "", fmt.Errorf("open %v error: %v", filePath, err)
	}
	defer file.Close()
	hash := md5.New()
	if _, err := io.Copy(hash, file); err != nil {
		return "", fmt.Errorf("copy md5 hash error: %v", err)
	}
	return hex.EncodeToString(hash.Sum(nil)), nil
}

```

## 代码测试

首先，先对代码进行简单测试。了解到底是哪个过程导致了协程被夯住。测试代码如下：

```go
func main() {

	fmt.Printf("\n[%v]begin openfile", time.Now())
	file, err := os.Open("./test.img")
	if err != nil {
		panic(err)
	}
	hash := md5.New()
	fmt.Printf("\n[%v]begin io.Copy()", time.Now())
	if _, err := io.Copy(hash, file); err != nil {
		panic(err)
	}

	fmt.Printf("\n[%v]begin hash.Sum", time.Now())
	b := hash.Sum(nil)

	fmt.Printf("\n[%v]begin hex.EncodeToString", time.Now())

	md5Info := hex.EncodeToString(b)
	fmt.Printf("\n[%v]md5Info: [%v]", time.Now(), md5Info)

}
```

测试流程：

0. 测试环境：华为云鲲鹏ARM服务器，64G内存，32核CPU。
1. 先使用生成300G文件的。`fallocate  -l 300G test.img`
2. 使用上述代码来获得结果。
3. 结果如下：
![测试结果](https://cdn.jsdelivr.net/gh/zlipsun/picstore/imgmd5.png)


## 解决方案

从上述测试结果可以看出主要的耗时过程为`io.Copy`。在查看`io.Copy`的源码后，发现是调用内部的`io.copyBuffer`方法。
正好方法内是使用了一个`for`循环来多次读取`buffer`。所以可以在内部加入一个`select`语句来进行超时判断。

改写方案
```go
// Enhancement of io.Copy in golang std lib
func CopyWithTimeout(dst io.Writer, src io.Reader, timeoutSeconds int) (written int64, err error) {
	return copyBufferWithTimeout(dst, src, nil, timeoutSeconds)
}

// Enhancement of io.copyBuffer in golang std lib
func copyBufferWithTimeout(dst io.Writer, src io.Reader, buf []byte, timeoutSeconds int) (written int64, err error) {
	// If the reader has a WriteTo method, use it to do the copy.
	// Avoids an allocation and a copy.
	if wt, ok := src.(io.WriterTo); ok {
		return wt.WriteTo(dst)
	}
	// Similarly, if the writer has a ReadFrom method, use it to do the copy.
	if rt, ok := dst.(io.ReaderFrom); ok {
		return rt.ReadFrom(src)
	}
	if buf == nil {
		size := 32 * 1024
		if l, ok := src.(*io.LimitedReader); ok && int64(size) > l.N {
			if l.N < 1 {
				size = 1
			} else {
				size = int(l.N)
			}
		}
		buf = make([]byte, size)
	}
	begin := time.Now()
	timeoutChan := make(chan struct{}, 1)
	endFromTimeout := begin.Add(time.Second * time.Duration(timeoutSeconds))
	for {
		nr, er := src.Read(buf)
		if nr > 0 {
			nw, ew := dst.Write(buf[0:nr])
			if nw < 0 || nr < nw {
				nw = 0
				if ew == nil {
					ew = errors.New("invalid write result")
				}
			}
			written += int64(nw)
			if ew != nil {
				err = ew
				break
			}
			if nr != nw {
				err = io.ErrShortWrite
				break
			}
		}
		if er != nil {
			if er != io.EOF {
				err = er
			}
			break
		}

		if time.Now().After(endFromTimeout) {
			timeoutChan <- struct{}{}
		}

		select {
		case <-timeoutChan:
			err = ErrTimeout(fmt.Sprintf("copy buffer time out, limit [%v] seconds", timeoutSeconds))
			return 0, err
		default:
		}
	}
	return written, err
}
```
