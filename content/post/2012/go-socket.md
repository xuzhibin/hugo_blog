---
title: Go Socket例子
date: 2012-12-12
categories:
- go
tags:
- socket
---
# Tcp Server (tcpserver.go)


``` go
package main

import (
    "fmt"
    "net"
    "os"
    "time"
)

func main() {
    service := ":1200"
    tcpAddr, err := net.ResolveTCPAddr("tcp4", service)
    checkError(err)
    listener, err := net.ListenTCP("tcp", tcpAddr)
    checkError(err)
    for {
        conn, err := listener.Accept()
        if err != nil {
            continue
        }
        go handleClient(conn)
    }
}

func handleClient(conn net.Conn) {
    defer conn.Close()
    daytime := time.Now().String()
    fmt.Fprintf(os.Stderr,"connect time:%s,client ip:%s\r\n",daytime,conn.RemoteAddr().String())

    _,err := conn.Write([]byte(daytime)) // don't care about return value
    checkError(err)
    fmt.Fprintf(os.Stderr,"send time:%s\r\n",daytime)
    // we're finished with this client
}
func checkError(err error) {
    if err != nil {
        fmt.Fprintf(os.Stderr, "Fatal error: %s", err.Error())
        os.Exit(1)
    }
}
```

# Tcp Client (tcpclient.go)

``` go
tcpclient 192.0.0.1:1200 | tcpclient baidu.com:80

package main

import (
    "fmt"
    "net"
    "os"
)

func main() {
    if len(os.Args) != 2 {
        fmt.Fprintf(os.Stderr, "Usage: %s host:port ", os.Args[0])
        os.Exit(1)
    }
    service := os.Args[1]
    tcpAddr, err := net.ResolveTCPAddr("tcp4", service)
    checkError(err)
    conn, err := net.DialTCP("tcp", nil, tcpAddr)
    checkError(err)
    _, err = conn.Write([]byte("HEAD / HTTP/1.0\r\n\r\n"))
    checkError(err)

    result := make([]byte, 1024)
    _,err = conn.Read(result)
    checkError(err)
    fmt.Println("receive from server:\r\n")
    fmt.Println(string(result))
    os.Exit(0)
}

func checkError(err error) {
    if err != nil {
        fmt.Fprintf(os.Stderr, "Fatal error: %s", err.Error())
        os.Exit(1)
    }
}
```
