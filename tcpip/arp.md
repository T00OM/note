# 1. ARP
<font color="red">点对点链路不使用ARP，因为两端的IP地址已确定</font>

## 简介
地址解析协议（Address Resolution Protocol）：将32位的IP地址映射为数据链路层使用的地址（以太网MAC地址）

## 工作流程
主机A和主机B在同一网段（直连网络），主机A要向主机B发送信息。
1. 主机A首先查询ARP表，确认是否有主机B对应的表项。确实存在，则可以直接利用ARP表中的MAC地址对IP数据报封装成帧。
2. 主机A的ARP表中无相应的表项，先缓存数据报文。然后以广播的方式发送ARP请求报文。ARP请求报文中的发送端MAC地址和IP地址为主机A的MAC地址和IP地址，而目的端的MAC地址为全零（说明MAC地址还未确定），IP地址为主机B的IP地址。该网段的所有主机均能接收到该报文，但是不予回应。（如果其他主机ARP表中有表项的IP地址与主机A的IP地址相同，但是MAC地址不同，将该表项更新）。
3. 主机B接受到主机A发出的ARP请求报文后，先将主机A的IP地址和MAC地址缓存在ARP表中，再以单播的方式发送应答报文，其中将包含自己的MAC地址。
4. 主机A接受到主机B发出的ARP应答报文之后，先缓存主机B的IP地址和MAC地址。利用主机B的MAC地址对IP数据报封装成帧。

## ARP表、ARP请求报文、应答报文
1. ARP表（ubuntu `arp -a`）
![ARP表](https://github.com/CookedFox/pics/blob/master/tcpip/arp3.png?raw=true)
2. ARP请求报文
![请求报文](https://github.com/CookedFox/pics/blob/master/tcpip/arp1.png?raw=true)
3. ARP应答报文
![应答报文](https://github.com/CookedFox/pics/blob/master/tcpip/arp2.png?raw=true)

## ARP欺骗
### 攻击过程
主机ABC在同一网段，AB为正常主机，C为中间人
1. 暴力扫描，具体可为`ping`该网段所有的ip。获取IP和MAC地址
2. 当目标询问网关时，主机C也应答。ARP表遵循后到优先，所以主机C能成功篡改IP和MAC。`sudo arpspoof -i wlp1s0 -t 192.168.1.100 192.168.1.1`
3. 可实现断网、截取数据流（要使目标正常上网，需要开启主机C的转发数据包的功能）
### 防御手段
动态ARP检测（<font color="red">TODO</font>）
静态ARP绑定

## 代理ARP
没有配置缺省网关（Default Gateway）的计算机要将数据包转发到其他网络中去，可以利用代理ARP技术实现。网关在收到源计算机的ARP请求后会使用自己的MAC地址和目标的IP地址对源计算机应答。（存在缺省网关，下一跳的IP地址就确定了，所以源计算机需要根据下一跳的IP地址查找MAC地址。而无缺省网关，发送的ARP请求中，目的IP地址就为要请求的外部网络IP地址，代理ARP功能会使网关对该ARP请求报文应答，条件是这个外部IP地址的网段在该网关的路由表中）
### 优点
1. 使没有配置缺省网关的计算机正常上网

### 缺点
1. 增加了某一网段ARP流量
2. 需要更大的ARP表
3. ARP欺骗风险

## 免费ARP
用于检测局域网内的IP地址冲突，源主机发出的免费ARP请求报文，其中目的IP地址为本机的IP地址。当收到应答报文时，就说明出现了IP地址冲突。地址冲突的主机会不断发送免费ARP，直到其它方让步，修改IP地址。