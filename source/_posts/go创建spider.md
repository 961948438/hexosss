---
title: go创建spider
date: 2021-06-15 00:41:04
tags:
  - go
categories:
  - go
---
## detail
  ### 正则表达式的使用步骤：首先编译我们的正则表达式，然后利用编译的正则表达式去匹配内容。
  ### 返回值类型处声明我们需要返回的变量，将来return会自动将这些变量返回的。
  ### go语言采用延迟调用加recover函数去捕获异常


```
package main

import (
	"fmt"
	"io"
	"net/http"
	"regexp"
	"strconv"
	"strings"
)

func spiderPageDetail(url string)(title string, content string, err error){
	defer func() {
		// recover内置函数，可以捕获到异常
		err := recover()
		if err != nil {
			fmt.Println("err:", err)
			return
		}
	}()
	response, errdetail := http.Get(url)
	var result string
	var buf []byte = make([]byte, 1024)
	if errdetail != nil {
		fmt.Println("PageDetail请求报错：", errdetail)
		err = errdetail
		return
	}
	defer response.Body.Close()
	for true {
		num, err2 := response.Body.Read(buf)
		if err2 != nil {
			if err2== io.EOF{
				break
			} else {
				fmt.Println("detail报错", err2)
				err = err2
				return
			}
		}
		result += string(buf[:num])
	}
	ret2 := regexp.MustCompile(`<h1 class="article-title"><a href=".*?">(?s:(.*?))</a></h1>`)
	ret3 := regexp.MustCompile(`<p>(?s:(.*?))</p>                </article>`)
	alltitles := ret2.FindAllStringSubmatch(result,-1)
	allconts := ret3.FindAllStringSubmatch(result,-1)
	title = alltitles[0][1]
	content = strings.Join(strings.Split(allconts[0][1],"<br>"),"")
	return
}

func spiderPage(i int){
	// 获取一个网页的所有内容：
	var url string = "https://duanzixing.com/page/" + strconv.Itoa(i) + "/"
	response, err := http.Get(url)
	if err != nil {
		fmt.Println("请求报错：", err)
	}
	defer response.Body.Close()
	buf := make([]byte, 1024)
	var result string
	for true {
		num, err2 := response.Body.Read(buf)
		if err2 != nil {
			if err2 == io.EOF{
				break
			} else {
				fmt.Println("错误信息：", err2)
			}
		}
		result += string(buf[:num])
	}

	ret := regexp.MustCompile(`<h2><a href="(?s:(.*?))" title="`)
	alls := ret.FindAllStringSubmatch(result,-1)
	for _, singleUrl := range alls{
		fmt.Println(singleUrl[1])
		title, content, err3 := spiderPageDetail(singleUrl[1])
		if err3 != nil {
			fmt.Println("报错：", err3)
			continue
		}
		fmt.Printf("标题：%s\t\t内容：%s",title,content)
	}

}

func toWork(start int, end int){
	fmt.Printf("正在爬虫第：%d页到第%d页的数据\n", start, end)
	for i := start; i <= end ; i++ {
		spiderPage(i)
	}
}

func main() {
	var start, end int
	fmt.Println("请输入爬虫起始页")
	fmt.Scan(&start)
	fmt.Println("请输入爬虫终止页")
	fmt.Scan(&end)
	if start <1 {
		start = 1
	} else if start> 92 {
		start =92
	}
	if end >92 {
		end = 92
	} else if end <1 {
		end = 1
	}
	if end > start {
		toWork(start, end)
	}
}
```