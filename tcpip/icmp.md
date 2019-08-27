## 简介
ICMP（`Internet Control Message Protocol`）Internet控制报文协议。是TCP/IP协议簇的一个子协议，无连接，用于传输出错报告控制信息。ICMP是TCP/IP模型中网络层的重要成员，有两种常用的网络管理命令使用到了ICMP，ping用来测试网络的可达性，traceroute用来显示到达目的主机的路径。
![icmp](https://raw.githubusercontent.com/CookedFox/pics/master/tcpip/icmp.png)
## 报文
1. ICMP报文有两种类型：差错报文和查询报文。
2. ICMP消息类型有十多种（ICMP报文首部的类型+代码决定），每种ICMP数据类型都被封装在一个IP数据报中。
3. ICMP报文包含在IP数据报中，是IP数据报的数据部分。
4. ICMP差错报文中的数据部分包含产生ICMP差错报文的IP数据报的首部和数据部分的前8个字节（无论是UDP还是TCP前八个字节都包含了端口信息。接收到ICMP差错报文的模块就会把它和特定的协议和用户进程联系起来）
![ICMP报文](https://raw.githubusercontent.com/CookedFox/pics/master/tcpip/icmpmessage.png)
---
ICMP差错报文
![ICMP差错报文](https://raw.githubusercontent.com/CookedFox/pics/master/tcpip/icmperrmessage.png)
## 不产生ICMP差错报文
1. ICMP差错报文（但是ICMP查询报文可能会产生ICMP差错报文）
2. 目的地址是广播地址或者多播地址的IP数据报
3. 作为链路层广播的数据报
4. 不是IP分片的第一片（不是第一片不包含端口信息）
5. 原地址不是单个主机的数据报。源地址不能为零地址、环回地址、广播地址、多播地址
<font color="red">防止广播风暴</font>
## ping
### 概要 
测试另一台主机是否可达的命令。该程序发送一份ICMP回显请求报文（echo request Type=8）给主机并等待ICMP回显应答（echo reply Type=0）
<font color='red'>一台主机的可达性可能不止取决与IP是否可达，还取决于使用何种协议以及端口号。（ping不同，但是telnet可远程登录）</font>
大多数的TCP/IP实现都在内核中直接支持ping服务器。对于其他类型的ICMP查询报文，服务器必须响应标识符和序号字段，另外，客户发送的选项数据也必须回显。
* unix系统在实现ping程序时把ICMP报文中的标识符字段设置成发送进程的pid，这样即使在同一台主机运行了多个ping程序实例，也能识别出服务器返回的信息。
* windows中，不管开多少个窗口ping，ICMP查询报文中的标识符都相同，包序列增加256。
### IP记录路由选项
大多数不同版本的ping程序都提供-R选项，以提供记录路由的功能。它使得ping程序发送出去的IP数据报中设置IPRR选项。这样，每个处理该IP数据报的路由器都它的出口IP地址放入选项字段中，当数据报到达目的端时，IP地址清单应该复制ICMP回显应答中，这样返回途中经过路由器的出口地址也被加入选项字段中，当ping程序收到回显应答时，它就打印这份IP地址清单。
**最大的问题在于IP首部只有有限的空间存放IP地址，IP数据报首部的选项大小为40个字节，RR选项需要用3个字节，所以最多只能存放9个地址**
![iprr](https://raw.githubusercontent.com/CookedFox/pics/master/tcpip/iprr.jpg)
![ping](https://raw.githubusercontent.com/CookedFox/pics/master/tcpip/ping.png)
### 类unix下的实现
在发送时将icmp标志设置为进程pid，同时icmp序列号由１递增，数据部分为发送时的时间，在icmp应答报文中将请求回显报文中的标志位和数据原封不动的返回，便于计算往返时延rtt。
## traceroute
### 操作
* traceroute使用ICMP报文和IP首部的TTL字段。TTL字段是由发送段初始设置的一个8位字段。推荐值为64,发送ICMP回显应答经常把TTL设置为最大值255。
* 路由器收到一份IP数据报时，如果TTL字段为0或者1,则路由器不转发数据报，路由器将它丢弃，并向源主机发送一份ICMP超时信息。（接受到这种数据报的目的主机可以将它交给应用程序，这是因为不需要转发该数据报。但是通常情况下，系统不应该接收TTL字段为0的数据报）
* traceroute程序发送一份UDP数据报给目的主机，但是它选择一个不可能的值作为UDP端口号（大于30000），目的主机的任何一个应用程序都不可能使用该端口。这样，当该数据报到达目的主机时，目的主机的UDP模块产生一份”端口不可达“ICMP报文。traceroute程序要做的就是区分接受到的ICMP报文是超时还是端口不可达。
### 注意事项
* traceroute程序将其发送的UDP端口号设置为进程号与32768之间的逻辑或值（按位或）
* 两份连续的IP数据报可能采用不同的路由
* 不能保证ICMP报文的路由和traceroute发送的UDP数据报采用同一路由
* 返回的ICMP报文中的源IP地址是UDP数据报到达的路由器接口的IP地址,记录的IP地址指的是发送接口地址。
## IP源站选路选项
### 概要
1. 严格的源路由选择。发送端指明IP数据报必须采用的确切路由。如果一个路由器发现源站路由所指定的下一个路由器不在其直接连接的网络上，那么它就返回一个”源站路由失败“的ICMP差错报文。
2. 宽松源站路由，发送端指明数据报经过的IP地址清单，但是数据报在清单上指明的任意两个地址之间可以通过其他路由器。

宽松源站路由选路code字段是0x83
严格源站选路为0x89
![ipsource](https://raw.githubusercontent.com/CookedFox/pics/master/tcpip/ipsource1.png)
### 操作机制
1. 发送主机从应用程序接受源站路由清单，将第一个表项去掉（它是数据报的最终目的地址），将剩余的项前移，并将原来的目的地址作为清单的最后一项，指针依然指向清单的第一项。
2. 每个处理数据报的路由器检查其是否为数据报的最终地址。如果不是则正常转发数据报。（这种情况下，必须指明宽松源站选路，否则不能接受该数据报）
3. 如果该路由器为最终目的，且指针不大于路径的长度，那么由ptr指定清单的下一个地址就是数据报的最终目的地址，由外出接口相对应的IP地址取代刚才使用的源地址，指针加4。
![ipsource](https://raw.githubusercontent.com/CookedFox/pics/master/tcpip/ipsource2.png)

## ICMP重定向
### IP搜索路由表
1. 搜索匹配的主机地址
2. 搜索匹配的网络地址
3. 搜索默认表项
### ICMP重定向差错
假定主机发送一份IP数据报给R1。
R1收到数据报并且检查它的路由表，发现R2是发送该数据报的下一站。当它把数据报发送给R2时，R1检测到它在发送的接口和数据报到达的接口是相同的。(这里值是R1的出口地址、R2的入口地址和主机为直连网络)
R1发送一份ICMP重定向报文给主机，告诉它以后要把数据报发送给R2而不是R1。
![re](https://raw.githubusercontent.com/CookedFox/pics/master/tcpip/icmpre.png)