# socket编程
## 初涉
socket编程可以简单理解为C/S架构的通信编程。
Socket又称"套接字"，应用程序通常通过"套接字"向网络发出请求或者应答网络请求，使主机间或者一台计算机上的进程间可以通讯。

### server端
```python

import socket

server = socket.socket()

server.bind(('localhost',6969)) # 绑定端口，bind只有一个参数,是元组 ('localhost',6969)

server.listen()

while True:
    conn,addr = server.accept()
    while 1:
        data = conn.recv(1024)
        if len(data) == 0:
            conn.close()
            print("关闭 {} 的链接".format(addr))
            break
        print("recv:",data)
        conn.send(data.upper())

    conn.close()


```
> 可以看到，在接收数据的时候，判断了数据的长度，若为空，则直接断开循环管理链接，原因是客户端关闭链接后，server端一直会收到空的请求。



### client端

```python
import socket

sock = socket.socket()

sock.connect(("localhost",6969))


while True:
    world = input(">>>")
    if len(world) != 0 and world != "exit":
        sock.send(world.encode())
        data = sock.recv(1024) #接收数据 1k buffer
        print("recv:",data)
    elif world == "exit":
        break
    else:
        continue

sock.close()
print("关闭终端")

```
