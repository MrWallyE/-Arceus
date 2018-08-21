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

<u>**为什么需要Socket？**</u>

- 普通的I/O操作过程

  打开文件->读/写操作->关闭文件

- TCP/IP协议被集成到操作系统的内核中，引入了新型的“I/O”操作

  1）不同的机器上的两个进程进行网络操作，如何连接？

  2）网络协议具有多样性，如何进行统一操作？

- 需要一种通用的网络编程接口：Socket



**<u>什么是Socket？</u>**

- 独立于具体协议的网络编程接口
- 在ISO模型中，主要位于会话层和传输层之间
- BSD Socket（伯克利套接字）是通过标准的UNIX文件描述符和其他程序通讯的一个方法，目前一斤被广泛移植到各个平台。



<u>**Socket类型**</u>

- 流式套接字（SOCK_STREAM）

  提供了一个面向连接、可靠的数据传输服务，数据无差错、无重复的发送且按发送顺序接收。

- 数据报套接字（SOCK_DGRAM）

  提供无连接服务。数据包以独立数据包的形式被发送，不提供无差错保证，数据可能被丢失，重复或失序。

- 原始套接字（SOCK_RAW）

  可以对较低层次协议，如IP、ICMP直接访问。



<u>**两类系统中使用的Socket**</u>

- Windows Socket（Winsock）
- Linux Socket（BSD Socket）



### Socket常用函数

#### Socket常用函数-基本函数

- 网络连接函数

  socket    创建套接字

  bind    绑定本机端口

  connect    建立连接

  listen    监听端口

  accept    接受连接

  recv，recvfrom    数据接收

  send，sendto    数据发送

  close，shutdown    关闭套接字

- 转换函数

  1）IP地址转换函数

  inet_addr()    点分十进制数表示的IP地址转换为网络字节序的IP地址

  inet_ntoa()    网络字节序的IP地址转换为点分十进制数表示的IP地址

  2）字节排序函数

  htonl    4字节主机字节序转换为网路字节序

  ntohl    4字节网络字节序转换为主机字节序

  htons    2字节主机字节序转换为网络字节序

  ntohs    2字节网络字节序转换为主机字节序



**网络字节次序和主机字节次序**

例子：在内存中双字0x01020304（DWORD）的存储方式

内存地址    4000 4001 4002 4002

LE                  04      03     02     01

BE                  01       02     03     04

网络字节顺序是TCP/IP中规定的一种数据表示格式，它与具体的CPU类型、操作系统等无关。

主机字节顺序与具体的CPU类型操作系统等**<u>有关！</u>**



#### **Socket常用函数-网络信息检索函数**

- 网络信息检索函数

  gethostname    获得主机名

  getpeername    获得与套接字相连的远程协议地址

  getsockname    获得套接字本地协议地址

  gethostbyname    根据主机名取得主机信息

  gethostbyaddr    根据主机地址取得主机信息

  getprotobyname    根据协议名取得主机协议信息

  getprotobynumber    根据协议号取得主机协议信息

  getservbyname    根据服务名取得相关服务信息

  getservbyport    根据端口号取得相关服务信息

  getsockopt/setsockopt    获取/设置一个套接字选项

  ioctlsocket    设置套接字的工作方式



Windows Socket的启动

使用Winsock API编制的网络应用程序中，在调用任何一个Winsock函数之前都必须检查协议栈的安装情况，使用函数WSAStartup()完成操作：

```c
int WSAStartup(

    WORD wVersionRequested,

    LPWSADATA lpWSAData

);
```

wVersionRequested是一个WORD型（双字节型）数值，指定使用的版本号，对Winsock2.2而言，此参数的值为0x0202，也可以用宏MAKEWORD(2,2)来获得

lpWSAData是一个指向WSADATA结构的指针，它返回关于Winsock实现的详细信息



#### 创建套接字socket()函数

应用程序在使用套接字通信前，必须要用有一个套接字，使用socket()函数来给应用程序创建一个套接字

```c
SOCKET socket(

    int af,

    int type,

    int protocol

);
```

<u>socket()参数说明:</u>

- af参数说明套接字接口要使用的协议地址族，地址族与协议族含义相同。如果想建立一个TCP或UDP，只能用常量AF_INET表示表示使用互联网协议（IP）地址。Winsock还支持其他协议，但一般很少使用。

- type参数描述套接字的类型，af是AF_INET的时候只能为SOCK_STREAM、SOCK_DGRAM或SOCK_RAW

- protocol说明该套接字使用的特定协议，当协议地址族af和协议类型type确定后，协议字段可以使用的值是限定的


|       协议       | 地址族  | 套接字类型 | 套接字类型使用的值 |         协议字段         |
| :--------------: | :-----: | :--------: | :----------------: | :----------------------: |
| 互联网协议（IP） | AF_INET |    TCP     |    SOCK_STREAM     |       IPPROTO_TCP        |
|       同上       |  同上   |    UDP     |     SOCK_DGRAM     |       IPPROTO_UDP        |
|       同上       |  同上   |    Raw     |      SOCK_RAW      | IPPROTO_RAW IPPROTO_ICMP |



#### 指定本地地址-bind()函数

当socket()创建了一个套接字后，需要将该套接字与该主机上提供服务的某端口联系在一起，bind()函数用于完成这样的绑定。

```c
int bind(
    SOCKET s,
    const struct sockaddr FAR * name,
    int namelen
);
```

<u>bind()参数说明：</u>

- s标识一个未绑定的套接字描述字，它是socket()函数调用成功是返回的值
- name是一个与制定协议有关的地址结构指针，储存了套接字的地址信息，Winsock中使用sockaddr_in结构指定IP地址和端口信息

```c
struct sockaddr_in{
    short sin_family;//一般为AF_INET，表示使用IP地址族
    u_short sin_port;//是以网络字节序表示的16位端口号
    struct in_addr sin_addr;//是网络字节序的32位IP地址
    char sin_zero[8];//一般不用，用0填充
}
```

- namelen表示地址参数(name)的长度
- IP地址参数为INADDR_ANY，则由系统内核来自动指定，port为0，则由系统指派一个1024~65535之间唯一的端口号



#### 服务器端启动监听-listen() 函数

- 在一个服务器端用socket()调用成功创建了一个套接字，并用bind()函数和一个指定的地址关联后，就需要指示该套接字进入监听连接请求状态，这需要通过listen()函数来实现

```C
int listen(
    SOCKET s,
    int backlog
);
```

<u>listen()参数说明：</u>

- s代表一个已经绑定了地址，但还未建立连接的套接字描述字
- backlog指定了正在等待连接的最大队列长度



#### 客户端请求连接-connect()函数

- 当服务器端建立好套接字并与一个本地地址绑定后，就进入监听状态，等待客户发出连接请求。在客户端套接字建立好之后，就调用connect()函数来与服务器建立连接。

```c
int connect(
    SOCKET s,
    const struct sockadde FAR * name,
    int namelen
);
```

<u>connect()参数说明：</u>

- s将要建立连接的套接字描述字
- name是一个指向远端套接字地址的结构（sockaddr_in）的指针，表示s套接字欲与其建立一条连接
- namelen是服务器端的地址长度，即name的长度



<u>connect()函数的说明：</u>

- 在客户端使用该函数请求建立连接时，将激活建立连接的三次握手，用来建立一条到服务器TCP的连接。如果调用该函数前没有调用bind()来绑定本地地址，则由系统隐式绑定一个地址到该套接字。
- 该函数用在UDP的客户端时，connect()函数并不是真正的发出建立请求连接的请求，调用将从本地操作系直接返回。这样可以将服务器的地址信息保存下来，在后续UDP端口发送数据时，由套接字自动在发送函数中填入服务器地址，而不需要由应用程序在调用发送函数时填入。



#### 服务器端接受连接-accept()函数

- 在服务器端通过listen()函数调用表示服务器进入监听客户的连接请求状态，而在服务器端调用accept()函数时表示可以接收来自客户端由connect()发出的连接请求，双方进入连接状态

```c
SOCKET accept(
    SOCKET s,
    struct sockaddr FAR * addr,
    int FAR * addrlen
);
```

<u>accept()参数说明：</u>

- s标识一个套接字，该套接字处于监听状态
- addr是一个地址结构的指针，用来存放发出连接请求的那个客户机的IP地址信息
- addrlen指出客户套接字地址结构的长度



<u>accept()函数说明：</u>

- 本函数从s的等待连接队列中抽取第一个连接，创建一个与s同类的新的套接字并返回句柄
- 该函数用于面向连接的服务器端，在IP协议族中只用于TCP服务器端



#### 发送数据-send()函数

- 在已经建立连接的套接字上发送数据，可以使用send()函数

```c
int send(
    SOCKET s,
    const char FAR * buf,
    int len,
    int flags
);
```

<u>send()参数说明：</u>

- s用于标识已建立连接的套接字
- buf是一个字符缓冲区，内有将要发送的数据
- len即将发送的缓冲区中的字符数
- flags用于控制数据传输的方式，0表示按正常的方式发送数据；宏MSG_DONTROUTE说明系统目标主机就在直接连接的本地网络中，无需路由选择；MSG_OOB指出数据是按带外数据发送的



<u>send()函数说明：</u>

send()函数适用于已建立连接的数据报或流式套接字发送数据，对于数据报类型套接字必须注意发送数据长度不大于通信子网的IP包最大长度



#### 接收数据-recv()函数

- 对于已建立连接的套接字来说，要从套接字上接收数据就要使用recv()函数。

```c
int recv(
    SOCKET s,
    char FAR * buf,
    int len,
    int flags
);
```

<u>recv()参数说明：</u>

- s为已建立连接的套接字
- buf为用于接收数据的缓冲区
- len为缓冲区的长度
- flags制定调用的方式。0标识接受的是正常数据，无特殊行为。MSG_PEEK表示会使用有用的数据复制到所提供的接收端缓冲区内，但是没有从系统缓冲区中将数据删除。MSG_OOB表示处理数据带外数据。



从无连接的套接字上接收数据-recvfrom()函数

- 对于无连接的套接字来说，要从套接字上接收一个数据报并保存发送数据的原地址，就要使用recvfrom()函数。

```c
int recvfrom(
    SOCKET s,
    char FAR * buf,
    int len,
    int flags,
    struct sockaddr FAR * from,
    int FAR * fromlen
);
```

<u>recvfrom()参数说明：</u>

- s标识一个套接字的描述字
- buf接收数据的缓冲区
- len接收数据缓冲区的长度
- flags调用操作方式，同recv()中的flags
- from可选指针，指向装有源地址的缓冲区
- fromlen可选指针，指向from缓冲区的长度值



<u>recvfrom()函数说明：</u>

该函数的用法与有连接时recv()的用法一致，要注意的是该函数也可以用于有连接时数据的接收。



#### 在无连接套接字上发送数据-sendto()函数

- 对于无连接的套接字来说，要从套接字上发送一个数据报，就要使用sendto()函数

```c
int sendto(
    SOCKET s,
    const char FAR * buf,
    int len,
    int flags,
    const struct sockaddr FAR * to,
    int tolen
);
```

<u>sendto()参数说明：</u>

- s本机的套接字
- buf待发送数据的缓冲区
- len指明buf缓冲区中要发送的数据长度
- flags调用方式标志位，同send()中的flags
- to可选指针，指向接收数据的目的套接字地址
- tolen是to所指的地址的长度



<u>sendto()函数说明：</u>

该函数的使用方式类似send()函数，当用于无连接套接字接口，调用函数前要设置，指出目标IP地址和目标端口号。如果用于有连接的套接字时，则不能指定目标地址和目标端口，将to设置为空，地址长度设置为0。当然在有连接的情况下很少使用该函数。



#### 关闭读写通道-shutdown()函数

- 在一个套接字上的读写操作完成后，应该首先使用shutdown()函数来关闭套接字的读通道、写通道或读写通道，这样做的好处是当双方不再有数据要发送或接收时，可以通知对方，以防止数据丢失，并能“优雅”地关闭连接。

```c
int shutdown(
    SOCKET s,
    int how
);
```

<u>shutdown()参数说明：</u>

- s标识一个套接字的描述字
- how是一个标志，用于描述禁止哪些操作，取值如下所示

|  关闭方式  | 参数值 |                             说明                             |
| :--------: | :----: | :----------------------------------------------------------: |
| SD_RECEIVE |   0    | 表示不允许再调用接收函数，它关闭读通道。套接字接收缓冲区中的所有数据都被丢弃，并且有新数据到达套接字时，也被TCP协议层丢弃，但它对发送缓冲区没有影响，进程仍然可以在套接字上发送数据。 |
|  SD_SEND   |   1    | 表示不允许再调用发送函数，它关闭写通道。在套接字发送缓冲区中的数据都被发送出去，得到接收端确认之后，就生成一个FIN包关闭连接。但它对接收缓冲区没有影响，进程仍然可以在套接字上接收数据。 |
|  SD_BOTH   |   2    |  关闭读写通道，相当于执行了上面SD_RECEIVE和SD_SEND两个命令   |



#### 关闭套接字-closesocket()函数

- shutdown函数只关闭读写通道，并不关闭套接字，且套接字所占有的资源将被一直保留至closesocket()调用之前。
- 一个套接字不再使用时一定要关闭这个套接字，以释放与该套接字关联的所有资源，包括等候处理的数据

```c
int closesocket(
    SOCKET s
);
```

<u>closesocket()参数说明：</u>

- s表示即将被关闭的套接字



#### Winsock-WSACleanup()函数

- 当应用程序不再使用Winsock API中的任何函数时，必须调用WSACleanup()将其从Windows Socket的实现中注销，以释放为此应用程序或DLL分配的任何资源。

```c
int WSACleanup(void);
```

<u>WSACleanup()函数说明:</u>

WSACleanup()函数是任何一个Winsock应用程序在最后必须要调用的函数。在一个多线程的环境下，WSACleanup()函数终止了Windows Sockets在所有线程上的操作。



### TCP/IP网络程序框架

#### 面向连接的C/S程序工作流程（TCP）

- 服务器端工作流程

  1）使用WSAStartup()函数检查系统协议栈安装情况

  2）使用socket()函数创建服务器端通信套接字

  3）使用bind()函数将创建的套接字与服务器地址绑定

  4）使用listen()函数使服务器套接字做好接受连接的请求准备

  5）使用accept()函数接收来自客户端由connect()函数发出的连接请求

  6）根据连接请求建立连接后，使用send()函数发送数据，或者使用recv()函数接收数据

  7）使用closesocket()函数关闭套接字（可以先使用shutdown()函数先关闭读写通道）

  8）Win服务器）最后调用WSACleanup()函数结束Winsock Sockets API

- 客户端工作流程

  1）使用WSAStartup()函数检查系统协议栈安装情况

  2）使用socket()函数创建客户端通信套接字

  3）使用connect()函数发出与服务器建立连接的请求（调用前可以不用bind()端口号，由系统自动完成）

  4）建立连接后，使用send()函数发送数据，或者使用recv()函数接收数据

  5）使用closesocket()函数关闭套接字

  6）Win客户机）最后调用WSACleanup()函数结束Winsock Sockets API



#### 无连接的C/S程序工作流程

- 无连接的数据报传输服务通信时，客户端与服务器端所使用的函数是类似的，其工作流程如下：

  1）使用WSAStartup()函数检查系统协议栈安装情况

  2）使用socket()函数创建套接字，以确定协议类型

  3）调用bind()函数将创建的套接字与本地地址绑定，确定本地地址和本地端口号

  4）使用sendto()函数发送数据，或者使用recvfrom()函数接收数据

  5）使用closesocket()函数关闭套接字

  6）Win客户机）最后调用WSACleanup()函数结束Winsock Sockets API

- 注意事项：

  - 通信的一方可以不用bind()绑定地址和端口，由系统分配
  - 不绑定IP地址和端口号的一方必须首先向绑定地址的一方发送数据
  - 无连接的应用程序也可以调用connect()函数，但是它并不向对方发出建立连接的请求，而是在本地返回，由内核将connect()中指定的目标IP地址和端口号记录下来，在以后的通信中就可以使用面向连接的数据发送函数send()和数据接收函数recv()
  - 无连接的数据包传输过程中，作为服务器的一方必须先启动
  - 无连接客户端一般不调用connect()，在数据发送前客户与服务器各自通过socket()和bind()建立了半相关，发送数据时除指定本地套接字的地址外，还需要指定接收方套接字地址，从而在数据收发过程中动态建立全连接



#### 阻塞通信与非阻塞通信

- 阻塞方式：套接字进行I/O操作时，函数要等待到相关的操作完成以后才能返回，对提高处理机的利用率不利，但编程简单。
- 非阻塞方式：套接字进行I/O操作时，无论操作成功与否，调用都会立即返回。
- 阻塞方式编程简单，一个套接字的默认操作模式为阻塞，可以调用函数ioctlsocket()进行设置。

