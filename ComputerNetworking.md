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



## FTP：文件传输协议

- 客户机与服务器之间传送文件

- client/server model

  client：初始化传输（either to/from remote）

  server：远处主机

- ftp：RFC 959

- ftp server：port 21



### 控制/传输连接

- FTP 客户在21号端口与服务器连接，使用TCP

- client 在控制连接被授权

- client 通过控制连接浏览服务器目录

- server 接到文件传输命令，打开第二个 TCP 连接

- 文件传输完毕后，关闭传输连接

- FTP server 维护状态：当前目录，认证状态

- 控制连接使用：“带外传输”

  server 打开单独的 TCP 传输文件（在21号端口连接，在20号端口传输文件）



### 命令，响应

Sample commands：

- 在控制信道发送ASCII格式命令
- USER username
- PASS password
- LIST 返回当前目录的文件列表
- RETR filename 下载指定文件
- STOR filename 上传文件至服务器当前目录



Sample return codes

- 状态码和简明说明（类似HTTP）

- 331 Username OK，

  password required

- 125 data connection

  already open；transfer starting

- 425 Can't open data connection

- 452 Error writing file



## 电子邮件

三个组成部分：

- 用户代理（User Agent）
- 邮件服务器（Mail Servers）
- 简单邮件传输协议：SMTP

### User Agent

- 书写，编辑，阅读邮件信息
- e.g.,Eudora，Outlook，elm，Mozilla，Thunderbird
- 外出及进来的邮件都保留在服务器

### Mail Servers

- 邮箱：mailbox

  保留用户邮件

- 邮件队列：message queue

  等待发送的邮件

### SMTP protocol

服务器之间交换邮件的协议

- client：发送邮件服务器或客户端

- server：接收邮件的服务器

- 使用TCP从客户端可靠的传输邮件信息到服务器

- 使用端口25

- 直接传送：发送服务器直接发送给邮件至接收服务器

- 传输的三个阶段：

  1）握手 handshaking（greeting）

  2）传输 transfer of messages

  3）关闭 closure

- command/response 交互模式

  命令 commands：ASCII 文本

  响应 response：状态码和简明说明

- 消息是7-bit ASCII 字符



后话

- SMTP 使用持久连接

- SMTP 消息采用（header & body）7-bit ASCII 编码

- SMTP server 使用

  CRLF . CRLF 判断消息的结束



与HTTP的比较

- HTTP：拉 pull

- SMTP：推 push

- 都使用ASCII

  command/response 进行交互，status codes

- HTTP：每个 object 都有响应消息

- SMTP：多个 objects 在同一个消息中发送



### 邮件消息格式

SMTP：交换电子邮件的协议

RFC 822：消息的标准文本格式：

- header lines，e.g.,

  To：

  From：

  Subject：

- body

  the “message”，ASCII character only



### 消息格式：多用途电子邮件扩展

- MIME：多用途电子邮件扩展，RFC 2045，2056
- 消息头部的附加行说明MIME内容类型



### 邮件访问协议

- SMTP：发送邮件到接收者服务器

- Mail access protocol：从邮件服务器获取邮件

  **1）POP：Post Office Protocol [RFC 1939]**

  认证（agent <--> server）和下载

  **2）IMAP：Internet Mail Access Protocol [RFC 1730]**

  更多的特性

  能够在服务器上对邮件做某些操作

  **3）HTTP：gmail，Hotmail，Yahoo！Mail，etc.**



<u>**POP3 protocol**</u>

**认证阶段**

client 命令：

- user：username
- pass：password

server 响应

- +OK
- -ERR



**事务阶段**

client：

- list：列出消息数量
- retr：根据消息序号检索消息
- dele：删除
- quit



<u>**POP3（more）and IMAP**</u>

More about POP3

- 使用“Download and delete”模式，一个UA访问后邮件不能被其他UA访问
- 使用“Download and keep”模式允许不同UA访问
- POP3 是无状态协议

IMAP

- 所有信息保留在一个地方：server

- 所有用户通过文件夹组织消息

- IMAP 中 sessions 中保持用户状态

  文件夹的名字

  文件夹和消息ID的对应关系



## DNS域名系统

Internet 主机，路由：

- IP 地址（32 bit）-> 用于数据报寻址
- 域名 -> 用于用户识别



DNS：Domain Name System 域名系统

- 分布式数据库 -> 具有层次结构的多个域名服务器
- 应用层协议 -> 主机，路由，域名服务器互相通信完成解析



### DNS 服务

- 主机名到IP地址的翻译
- 主机别名
- 邮件服务器别名
- 负载均衡



### 分布式/层次化的数据库

**为什么不是集中化DNS？**

- 单点错误
- 通信负载
- 延迟
- 维护



**例子：一个希望获得 www.amazon.com IP 的 client A.**

- client A 查询 root server 获得 com DNS server
- client A 查询 com DNS server -> 获得amazon.com DNS server
- client A 查询amazon.com DNS server  -> 获得www.amazon.com IP地址



#### 根域名服务器（Root name server）

- 本地域名服务器（local name server）与根域名服务器联系

- 根域名服务器：

  1）与权威域名服务器联系完成域名映射

  2）返回给本地域名服务器结果



#### 顶级域名服务器（Top-level domain，TLD）

- 负责com，org，net，edu，etc，以及所有区域顶级域名uk，fr，ca，jp
- Network Solutions 维护 com TLD
- Educause 维护 edu TLD



#### 权威域名服务器（Authoritative Servers）

- 一个单位的域名服务器，为本单位负责服务器与IP地址之间的映射（e.g., Web, mail）
- 由本单位维护或者ISP维护



#### 本地域名服务器（Local Name Server）

- 不是严格的属于域名服务器的层次结构
- 每个ISP（区域ISP，大学，公司）都有自己的LNS（也称“默认域名服务器”）
- 当主机进行DNS查询，查询转发给LNS



### 迭代查询

- LNS依次查询域名服务器层次结构中的各个服务器最终获得该主机地址

### 递归查询

- 将查询的负担完全交给了查询对象



### Caching和记录更新

- 一个 DNS server 了解到域名映射后，它会caches映射

  1）cache 有一定的timeout时间

  2）TLD servers 通常会在 LNS cache保存

- #### Update/notify 机制的设计在IETF文档中有说明（RFC 2136）



### DNS 记录

分布式存储数据库记录（resource records，RR）

RR format：（name，value，type，ttl）

- Type=A

  name 是主机名

  value 是地址

  

- Type=CNAME

  name 是某台主机的别名

  value 是权威的名字

  

- Type=NS

  name 是域名

  value 是本域的权威域名服务器

  （维护用）

  

- Type=MX

  value 是邮件服务器的名字



### DNS 协议

query 和 reply 消息，具有相同的格式



**msg header**

- identification：16 bit

  用于标识 query，reply 时检索那一次查询

- flags

  query or reply

  recursion desired

  recursion available

  reply is authoritative



### DNS安全性

#### DDoS攻击

- 流量轰击根服务器

  Traffic Filtering

  Local DNS cache

- 流量轰击 TLD 服务器潜在危险性更强



#### 重定向攻击（Redirect attacks）

- Man-in-middle

  查询劫持

- DNS 污染

  发送虚假 DNS 响应



#### 利用DNS进行DDoS攻击

- 发送虚假的回复IP 地址



## P2P应用

### 纯P2P架构

- 没有服务器
- 端系统之间直接通信
- 端系统：经常改变IP间歇性地连接

### 应用方向

- 文件分发
- DHT



#### 文件分发：BitTorrent

- Peer 加入 torrent

  1）开始没有 chunks，但是慢慢从其他地方下载积累

  2）在 tracker 注册获得peers 列表

  3）与相邻的peers（“neighbors”）取得联系

- Peer downloading，同时 uploading chunks 给其他 peers

- Peer 加入离开时动态

- Peer 下载完成，可能离开（selfishly）或继续（altruistically）

<u>**Pulling Chunks**</u>

- 不同peers具有不同的文件chunks
- 周期性的，peer（Alice）问相邻peer获得chunks列表
- Alice发送缺失的chunks列表（rarest first 最罕见优先）

**<u>Sending Chunks：tit-for-tat</u>**

- Alice发送chunks给四个速度最快的邻居

- every 10 secs 

  重估top 4

- every 30 secs

  随机选择一个节点加入传输

  -可能成为最快的top 4

  -“optimistically unchoke”



#### Distributed Hash Table（DHT）

Hash Table

- 能够方便的存储和搜索数值表示的键值
- key = hash（original key）

Distributed Hash Table

- 在数百万的peer间分配（key，value）对

  键值对在peers间分配均匀

- 所有peer能够query在数据库中查询关键字

  数据库返回key的value

  查询过程中，相关的peers节点间交换少量的消息

- 每个peer仅知道几个其他的peer节点

- Peer能够加入和离开（churn）



给peers分配key-value对

- 规则：将key-value对分配给具有最近ID（closest ID）的peer节点
- 惯例：最近的peer节点是有key的（immediate successor）



## Socket编程