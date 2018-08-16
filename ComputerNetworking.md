# 应用层



##  应用架构

- Client-server
- Peer-to-peer(P2P)
- Hybrid of client-server and P2P



### Client-server 架构

**Server：**

- always-on 主机
- 永久IP地址
- 可扩展：服务器集群

**Clients：**

- 与服务器通信
- 可能是间歇性的连接
- 可能是动态IP地址
- 通常相互之间不直接通信



### 纯P2P架构

- 没有always-on服务器
- 任意端节点主机之间可以相互通信
- 端节点间歇性的连接、相互通信，具有动态的IP地址



### 混合架构

例如：

**Skype**

- voice-over-IP  P2P应用
- 中央服务器：finding address of remote party
- client-client 连接：direct（not through server）

**Instant messaging（QQ）**

- 用户之间聊天是P2P
- 中央服务器：上线时在中央服务器获取IP地址；联系中央服务器获得好友的IP地址



## 进程通信

**Process：**运行于某个主机上的程序

**Client process：**发起通信的进程

**Server process：**等待被访问的进程

（P2P架构的应用程序同时有client process & server process）



### 进程寻址

- 主机有32-bit IP地址

- 进程接收消息需要一个标识符：

  1，进程号

  2，与主机上进程相应的**端口号port numbers**（16bit）

  > 例如：Example port number：
  >
  > HTTP server：80
  >
  > Mail server：25



### 套接字Socket（相当于一类通信函数调用）

- 进程 sends/receives messages 通过socket进行

- socket 类比于一扇门

  1，sending process 把 message 扔出门口

  2，sending process 依赖于传输层将 message 发送到接收的进程

- 其实就是函数调用

- API：（1）可以选择传输层协议；（2）指定某些参数



### 应用层协议的定义

- 交换消息的类型
- 消息类型的语法
- 字段的语义
- 进程发送和相应消息的规则

**协议的分类**

公有协议（Public-domain protocols）

- 在RFCs中定义
- 允许互操作
- e.g.，HTTP，SMTP

私有协议（Proprietary protocols）

- e.g.,Skype,QQ



### 应用所需要的传输层服务

**数据的丢失 Data loss**

- 一些应用（e.g.,audio）能忍受数据的丢失
- 某些应用程序（e.g.,telnet，file transfer）要求100%可靠的数据传输

**时效性 Timing**

- 一些应用（e.g.,Internet telephony，interactive games）要求低延迟

**吞吐率 Throughput**

- 一些应用（e.g.,multimedia）要求最小的吞吐率带宽
- 某些应用（“elastic apps”）随便多少带宽都可以工作

**安全性 Security**

- Encryption，data integrity，…



### Internet传输协议服务

**TCP service：**传输控制服务

- 面向连接 connection-oriented：

  setup required between client and server process

- 可靠的 reliable transport：

  between sending and receiving process

- 流控 flow control：

  sender won't overwhelm receiver

- 拥塞控制 congestion control:

  throttle sender when network overloaded

- 不提供：

  timing,minimum bandwidth guarantees

**UDP service：**用户数据报服务

- 不可靠的数据传输：

  unreliable data transfer between sending and receiving process

- 不提供：

  connection setup，reliability，flow control， congestion control，timing，bandwidth guarantees

## Web and HTTP

- Web page 由 object 组成
- Object 可以是 HTML 文件，JPEG图片，Java applet，声音文件，…
- Web page 一般是基 HTML 文件，包含很多索引对象
- 每个 object 由 URL 寻址（URL = host name + path name）

### HTTP概览

HTTP：超文本传输协议（hypertext transfer protocol）

- Web 应用层协议

- client/server model

  **client：浏览器**

  发送请求，接收，显示（渲染）Web objects

  **server：Web 服务器**

  回复针对请求的响应



使用TCP：

- Client 初始化TCP连接（生成socket）至服务器，端口80
- Server 接受 client 的TCP连接请求
- HTTP消息（应用层协议消息）在 browser（HTTP client）和 Web server（HTTP server）之间交换
- TCP连接关闭



HTTP 是无状态的：

即 server 不维护过去 client 的 request



### HTTP 连接



#### 非持久连接HTTP

- 一次TCP连接最多发送一个object
- HTTP 1.0



#### 持久连接HTTP

- 一次TCP连接可以发送多个object
- HTTP 1.1
- 大部分浏览器默认支持



### HTTP 消息

- 两种类型的 HTTP 消息：request，response



#### HTTP request message（请求消息）

由request line + header lines + entity body组成

request line由method URL HTTP版本组成



使用POST/GET method 上载 input：

1，POST method：

- Web page通常包含form input
- Input 包含在 request message 中的entity body

2，GET method：

- 使用URL

- Input包含在request line中的URL字段

  （/xxx?xx&xx）



方法类型：

HTTP/1.0

- GET

- POST

- HEAD

  ask server to leave requested object out of response



HTTP/1.1

- GET，POST，HEAD

- PUT

  在entity body包含上传文件路径为URL field指定

- DELETE

  删除URL field制定的文件



#### HTTP response message（响应消息）

由status line + header lines

status line由HTTP版本 HTTP响应状态码组成



HTTP 响应状态码：

- 200 OK

  request succeeded，requested object later in this message

- 301 Moved Permanently

  requested object moved，new location specified later in this message（Location：）

- 400 Bad Request

  request message not understood by server

- 404 Not Found

  requested document not found on this server

- 505 HTTP Version Not Supported



### 用户端状态：cookies

Cookies的使用非常广泛！

四个组成部分：

- HTTP 响应消息头部
- HTTP 请求消息头部
- 保留在客户端的 cookie 文件，由用户浏览器管理
- Web 站点后台数据库



用途：

- 认证（authorization）
- 购物车（shopping carts）
- 推荐（recommendations）
- 用户会话状态（Web e-mail）



### Web cashes（proxy server代理服务器）

目的：不访问 origin server 而满足 client request

- 用户可以设置 browser：Web 通过 cache访问

- browser 会发送所有 HTTP requests 给 cache

  若 Cache 命中：

  cache 返回 object

  否则：cache 向 origin server 请求 object，返回 object ，同时缓存



代理缓存

- cache 可以运行在客户端和服务器端
- 通常 cache 由 ISP 部署

