---
title: go的socket应用
date: 2021-06-15 00:48:53
tags:
  - go
categories:
  - go
---


##  服务端套接字创建

```
//  聊天室：服务端

package main

import (
	"fmt"
	"io"
	"net"
	"strings"
	"time"
)

type Client struct {
	C chan string
	Name string
	Addr string
}

var message chan string = make(chan  string)

var onlineMap map[string]Client

func handleConnect(con net.Conn)  {
	var hasData chan bool = make(chan bool)
	var quit chan bool  = make(chan bool)
	defer con.Close()
	addr :=  con.RemoteAddr().String()
	var clint Client = Client{
		C:    make(chan string),
		Name: addr,
		Addr: addr,
	}
	onlineMap[addr] = clint
	go WriteMsgClient(clint, con)
	message <- makeMsg(clint, "login")

	go func() {

		buf := make([]byte, 1024)
		for true {
			n, err := con.Read(buf)
			if err != nil {
				if err == io.EOF {
					fmt.Println("读取完毕！")
					return
				} else {
					delete(onlineMap, clint.Addr)
					fmt.Println("报错: ", err)
					quit <- true
					return
				}
			}
			msg := string(buf[:n])
			if msg == "online" && len(msg) == 6 {
				con.Write([]byte("user list:"))
				for _, client := range onlineMap {
					userinfo :=  client.Addr + ":" + client.Name + "\n"
					con.Write([]byte(userinfo))
				}
			} else if len(msg) >=8 && msg[0:7] =="rename|" {
				name := strings.TrimSpace(msg[8:])
				clint.Name = name
				onlineMap[addr] = clint
				clint.C <- makeMsg(clint, "更名成功！")
			} else {
				message <- makeMsg(clint,string(buf[0:n]))
			}
			hasData <- true
		}
	}()

	for true {
		select {
			case <- quit:
				delete(onlineMap, clint.Addr)
				message <- makeMsg(clint, "用户退出！")
				return
			case <- hasData:

			case <- time.After(time.Second * 45):
				delete(onlineMap, clint.Addr)
				message <- makeMsg(clint, "超时退出！")
				return
		}
	}
}

func  makeMsg(clint Client,msg string) string {
	buf := "\n【" + clint.Addr + "】" + clint.Name +"\t" + msg
	return  buf
}

func Manager () {
	onlineMap = make(map[string]Client, 1)

	for true {
		msg := <- message
		for _, client := range onlineMap {
			client.C <- msg
		}
	}
}

func WriteMsgClient(clint Client,con net.Conn){
	for msg := range clint.C {
		con.Write([]byte(msg))
	}
}

func main() {
	server, err := net.Listen("tcp","localhost:8081")
	if err != nil {
		fmt.Println("出错：", err)
		return
	}
	defer server.Close()
	fmt.Println("服务端tcp套接字创建完成：")
	go Manager()
	for true {
		con, err := server.Accept()
		if err !=nil {
			fmt.Println("出错：", err)
			return
		}
		go handleConnect(con)
	}
}



```


## 客户端套接字创建

```
// 并发聊天室客户端

package main

import (
	"fmt"
	"net"
)

func main() {
	con, err := net.Dial("tcp","localhost:8081")
	if err != nil {
		fmt.Println("报错：",err)
		return
	}
	defer con.Close()
	var msg []byte = make([]byte, 1024)
	go func() {
		var info string
		for true {
			_, err := fmt.Scanln(&info)
			if err != nil {
				fmt.Println("报错：", err)
				return
			}
			con.Write([]byte(info))
		}
	}()
	for true {
		num, RErr := con.Read(msg)
		if RErr != nil {
			fmt.Println("出错",err)
			return
		}
		fmt.Println(string(msg[0:num]))
	}
}

```