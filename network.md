# 计算机网络

应用层  网络层 传输层 链入层 物理层

以五层模型讲

# 一、计算机网络概要



![image-20200322161442697](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140259.png)

网络的核心 ：国家或全球的ISP（网络服务提供商） 主要设备是路由器和交换机

网络的端：家庭网络、公司网络等

如何连接到网络？

1. DSL 电话线
2. HFC光纤同轴
3. FTTH光纤到户
4. 以太网 用在机构或者学校

![image-20200322161856804](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140301.png)

这条线频率1MHZ

0-40kHZ 电话

40k-50kHZ 上传

50k-1MHZ 下载   上传和下载速度不对称

![image-20200322162238987](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140314.png)



当前国家主推：光纤入户

![image-20200322162335198](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140318.png)

学校、机构、网吧主推： 基于交换机路由器建立的一个局域网

![image-20200322162506905](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140321.png)



# 二、OSI七层参考模型

每个业务抽象化为1层，比较独立，只有相邻的两层会发生少量的数据交换

![image-20200322163518554](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140325.png)

osi太理想 设计不合理

自下而上说

![image-20200322164719737](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140327.png)

现在有TCP/IP四层协议

![image-20200322164853245](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140332.png)

## 数据封装过程

![image-20200322182339144](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140338.png)

层和层之间有数据的封装和拆分

![image-20210403145413944](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210403145416.png)![image-20210403145430569](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210403145432.png)

## 网络协议的组成要素

- 语法：数据和控制信息的格式
- 语义：发出何种控制信息做出何种动作以及做出何种响应
- 同步：以怎么样的方式发生

## 分组交换

![image-20210403145733539](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210403145734.png)

![image-20200322183118287](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140342.png)

路由器收到第一个包先存在路由器 直到最后一个包进入 判断信息对错后 发送

![image-20200322183317626](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140348.png)

- 发送时延：主机向网络发送数据的时间
- 传播时延：数据在网络上从这边到达那边的时间 
- 处理时延：在每个路由器或者交换机上需要检验信息对错
- 排队时延：在路由器上的排队时延

主要的时延是传播时延和排队时延

传播时延， 处理时延， 输入输出排队时延 

当输入队列满了之后，发生分组丢失，丢包后超时重传

路由器的输入输出缓存器一定，当传输的数据过多时，缓冲器存放不下，主动丢包，tcp需要重新传输

![image-20210403153204932](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210403153207.png)

## 电路交换

![image-20200502183210314](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140504.png)

线路被占用其余的就不能用了   更安全

分组交换是在线路上发包，通过包的信息选择到哪去

分组交换和电路交换的对比

分组网络

- 带宽共享
- 成本更低
- 更好实现

电路交换

- 更加安全
- 更难实现



## 频/时分复用  

DSL 电话线做网线就是一种频分复用0451

时分复用  分时

![image-20200502183621568](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140509.png)



## 路由选择协议

![image-20210403152332244](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210403152335.png)

一个路由器在网络上有好多个出口，一个出口对应一条输出链路

通过传来的目的IP  在路由器转发表中寻找最长前缀匹配  将数据包转发到相应的链路

路由表是实时更新的，动态的

## 吞吐量

![image-20210403153252427](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210403153254.png)



# 1.应用层

体系结构：

1. cs结构

2. p2p结构


![image-20200502190659742](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140513.png)

![image-20200502190745524](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140517.png)

百度云和迅雷就是p2p

p2p特点：

- 两方主机对等 有良好的扩展性
- ISP不友好
- 安全性差
- 不合适的激励

进程通讯时，cs和p2p均有服务端和客户端

socket是进程和网络之间的API

应用程序与传输层：选择网络协议/设定参数  其余透明

![image-20200502191743360](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140521.png)

TCP：

- 面向连接的服务
- 可靠的数据传输

UDP：

- 不提供必要服务的轻量级运输服务
- 不可靠的数据传输
- 不做流量控制和拥塞控制

![image-20200502192631053](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140524.png)

## web与http协议

http:hypertext transfer protocol超文本传输协议

客户程序和服务程序运行在不同的端系统，通过交换http报文进行会话

web：按需操作

- web页面
- 对象：图片 电影 各种等
- html基本文件：网页源码
- url：统一资源定位符    www.baidu.com ---->百度的ip
- web浏览器
- web服务器

## http请求-响应行动

http是基于TCP的

![image-20200502193338946](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140527.png)

![image-20200502193534316](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140529.png)

![image-20200502193607414](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140535.png)

长短连接  http用短连接

RTT往返时间

![image-20200502193758139](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140538.png)

![image-20200502193858677](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140543.png)

一般请求报文的实体主体都是空的

请求方法：get post（提交表单）  head（测试用）  put（上传）  delete

![image-20200502194244088](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140552.png)

![image-20200502194411935](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140546.png)

2正常  3服务器问题   4资源问题       5其他问题

## cookie

![image-20200502194605280](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140558.png)

![image-20200502195229809](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140601.png)

以前未登录过任何网站，但是你的cookie是固定的 一旦登录一次 这个cookie就跟这个特定的人绑定在一起了 信息泄露

## web缓存器



![image-20200502195402831](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140608.png)

![image-20200502200255676](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140605.png)

双十一，我在哈尔滨访问淘宝，当我的请求到了网络运营商的机房时，可能在这设置了web缓存器，机房去淘宝将请求的数据拿过来，再打包发给我。如果附近的人也访问了淘宝的同一个页面，那么他的请求就只是到了这个机房

存储的资源分为冷热 高速缓存 高速ssd 普通硬盘等  资源在存储介质上的上浮和下沉也很有讲究 LRU/LFU等缓存机制

web缓存器会定时更新资源 以保证是最新的

## http和https协议

![image-20200502200526985](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140613.png)

![image-20200502200638075](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140615.png)

![image-20210403161733518](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210403161737.png)

http2：多个response用同一个tcp连接 多路复用

![image-20210403161847824](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210403161851.png)





## ftp协议 

通常的ftp为TCP实现 也有UDP实现

文件传输协议

![image-20200503141625277](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140619.png)

![image-20200503141832195](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140620.png)

- 控制连接为长连接

- 数据连接为短连接


某些ftp用udp实现  确认+重传

![image-20200503142120996](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140623.png)

![image-20200503142148951](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140626.png)

## smtp简单邮件传输协议

![image-20200503142539701](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140628.png)

用户b的服务器不会主动向b代理发送邮件信息  由b代理定时访问b服务器  当有信息时抓取下来

IMAP 网络邮件访问协议

## dns服务

域名系统服务

域名<--->ip地址

一个ip可以有多个域名

一个域名只对应一个ip地址

![image-20200503144034389](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140631.png)

我们采用分布式 层级 dns   解析域名

![image-20200503144100194](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140633.png)

![image-20200503144329939](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140635.png)

顶级dns：.cn   .com等

权威dns：阿里等

本地dns：路由器就可以做域名解析

dns污染----将域名重定向其他ip

![image-20200503145139258](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140637.png)



递归和迭代

本地间的dns为递归

本地dns与其他更高级的服务器为迭代

递归：

![image-20210403170607399](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210403170640.png)

迭代：

![image-20210403170637911](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210403170643.png)

DNS记录

![image-20200503150130637](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140644.png)



# 2.运输层

提供主机上进程间的通信   tcp/udp  在ip之上通过端口通信

![image-20200503151743781](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140647.png)

将端系统之间的交互扩展为端系统之上进程间的交互

访问同一个端口

TCP有应答，回复发送机收到消息  短信等

UDP无应答，网络利用率高，游戏视频等

自己家：IP相当于楼的号 找到楼   门牌号相当于端口，有应用程序住在里面，那么这个门牌号有效，需要用TCP和UDP

![image-20210403171737269](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210403171741.png)

运输层报文段：

![image-20210403171755073](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210403171756.png)

![image-20210403171828101](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210403171850.png)

![image-20210403172006367](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210403205714.png)

两个ip  两个端口的四元组

![image-20210403172031010](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210403172032.png)

![image-20200503152310493](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140649.png)

tcp需要建立连接 维护更多的变量 占用更多的资源  可靠性：超时重传

![image-20210403172841784](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210403172843.png)

ssh通过TCP建立的

![image-20210403205245580](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210403205246.png)

1. 发起连接
2. 对连接的确认
3. 对确认的确认

![image-20210403205227841](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210403205249.png)

TIME_WAIT ：30s-2min不等





## 可靠传输协议rdt

可靠传输原理：

![image-20200503152626909](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140651.png)

我们只有不可靠的信道，所以需要用可靠的数据传输协议

![image-20200503152812157](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140655.png)

信道完全可靠

![image-20200503153045506](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140658.png)

![image-20200503153325904](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140700.png)

corrupt：冲突

- ACK：确认
- NAK：否认

信道有校验

包丢了怎么办？？没法校验

发了两个包 ack是属于那个包的？

![image-20200503153803574](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140706.png)

![image-20200503153817087](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140703.png)

上面ACK不确定是谁的

有序号

![image-20200503154253842](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140711.png)

![image-20210403211107471](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210403211108.png)

取消nak信号   发0包出错时 应答1包的ack表示出错   tcp协议没有nak信号



![image-20200503154408327](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140715.png)

比特交替协议  对带宽的使用率很低

有计时器  

计时器+ack+序号

滑动窗口法、dbn、回退n步法等自行了解

**现在用的TCP：计时器+ack+序号**



## tcp协议

![image-20200503155213948](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140719.png)

tcp报文  一行4Bytes   tcp头部一般是20Byte

![image-20200503155316390](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140722.png)

- 序号：第一个序号随机出来的 后面的序号为原始序号+发送的长度   比如第一个序号为731 发送了1个字节 下一个序号为731+1 = 732 以此类推
- 确认号：下次发的包的序号 seq + size
- 首部长度：4bit  16 TCP报文头部最多支持有16个32位字
- URG紧急数据   平常的数据包是按顺序发送的 如果上一个包没收到 这个包就不会收 紧急数据直接提交
- ACK对接受到包的确认
- PSH立即上交
- RST：connect refused 对方端口未打开会发送这个包
- SYN   tcp三次握手
- FIN   tcp四次挥手  FIN-ACK FIN-ACK
- 接收窗口   16bit 当前窗口大小 缓存区相关数据 剩余大小

![image-20200503152310493](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140649.png)

![image-20200503160600118](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140725.png)



![image-20200503161118376](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140728.png)

- 样本往返时间：真实往返时间  数据发送出去到接收到
- 估算往返时间：估算时间从一开始就有 一直积累 用来计算timeout
- 偏差

timeout一直是动态的

timeout的计算方法

超时时timeout*2

快速重传  收到3个冗余的ack信号  直接重传 不需要等待timeout

![image-20200503161839046](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140730.png)

rwnd接收窗口 已经读过的数据 搭配TCP报文的接收窗口

- 接收端:rwnd = RecvBuffer - （上次接受的 - 上次读走的）
- 发送端:上次发送的 - 上次确认的（指已经收到的） = 还在路上的 <= rwnd 否则丢包

 ![image-20200503162440170](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140734.png)

![image-20200503162609965](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140736.png)

![image-20200503162732001](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140739.png)

![image-20200503162825366](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140740.png)



![image-20200503162945601](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140744.png)

1. 端到端的拥塞控制  运输端
2. 网络辅助的拥塞控制 网络层



![image-20200503163109886](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140746.png)

网络层

32：1     数据信元：RM

- 数据信元：EFCI 1bit 当数据包经由的路由器的缓冲队列很满时会将此位置为1  接下来所有他经过的路由器就都知道这条通路很堵了
- 资源管理信元：管理信源经过每个路由器后所有的数据都会修改 到达对端后在发送回来 形成反馈

运输层：

拥塞窗口 ：一次往网络发送多少数据

![image-20210403215501979](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210403215505.png)



![image-20200503180711447](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140750.png)

![image-20200503180926048](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140748.png)

mms：最大报文长度

![image-20200503181351627](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140753.png)

![image-20200503181508099](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140758.png)

tcp发送端：

![image-20200503181656608](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140801.png)



#### 为什么建立连接是三次握手，而关闭连接却是四次挥手呢？

这是因为服务端在LISTEN状态下，收到建立连接请求的SYN报文后，把ACK和SYN放在一个报文里发送给客户端。而关闭连接时，当收到对方的FIN报文时，仅仅表示对方不再发送数据了但是还能接收数据，己方也未必全部数据都发送给对方了，所以己方可以立即close，也可以发送一些数据给对方后，再发送FIN报文给对方来表示同意现在关闭连接，因此，己方ACK和FIN一般都会分开发送。

#### 为什么TIME_WAIT状态需要经过2MSL(最大报文段生存时间)才能返回到CLOSE状态？

原因有二：
 一、保证TCP协议的全双工连接能够可靠关闭
 二、保证这次连接的重复数据段从网络中消失

先说第一点，如果Client直接CLOSED了，那么由于IP协议的不可靠性或者是其它网络原因，导致Server没有收到Client最后回复的ACK。那么Server就会在超时之后继续发送FIN，此时由于Client已经CLOSED了，就找不到与重发的FIN对应的连接，最后Server就会收到RST而不是ACK，Server就会以为是连接错误把问题报告给高层。这样的情况虽然不会造成数据丢失，但是却导致TCP协议不符合可靠连接的要求。所以，Client不是直接进入CLOSED，而是要保持TIME_WAIT，当再次收到FIN的时候，能够保证对方收到ACK，最后正确的关闭连接。

再说第二点，如果Client直接CLOSED，然后又再向Server发起一个新连接，我们不能保证这个新连接与刚关闭的连接的端口号是不同的。也就是说有可能新连接和老连接的端口号是相同的。一般来说不会发生什么问题，但是还是有特殊情况出现：假设新连接和已经关闭的老连接端口号是一样的，如果前一次连接的某些数据仍然滞留在网络中，这些延迟数据在建立新连接之后才到达Server，由于新连接和老连接的端口号是一样的，又因为TCP协议判断不同连接的依据是socket pair，于是，TCP协议就认为那个延迟的数据是属于新连接的，这样就和真正的新连接的数据包发生混淆了。所以TCP连接还要在TIME_WAIT状态等待2倍MSL，这样可以保证本次连接的所有数据都从网络中消失。



scp 基于ssh的安全拷贝   拷贝到远程主机  ssh基于tcp连接

# 3.网络层

![image-20200505182636046](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140807.png)

![image-20200505182925085](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140809.png)

MAC地址：网卡地址  物理地址

某厂的厂家代码是固定的

MAC地址理论上应该唯一 但是商家可以不按规矩来  还可以用虚拟的MAC地址

在连接主机时需要在局域网中找到对应IP的MAC地址物理主机

MAC地址和ip地址可以相互转换 ARP协议

ARP协议把ip地址转换为MAC地址  arp -a

![image-20200505183523708](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140812.png)

192开头一般是c类地址

**192.168.0.24/16   16表示前16位为网络位**   c类地址网络位可以不是24位的

NetMask网络掩码    c类地址网络掩码255.255.255.0

网络掩码&ip地址 得到网络号    

可以用网络掩码判断访问的ip地址是否是此局域网的，如果不是，需要经过网关（gateway）----本地路由ping baidu.com

这条命令的ip地址不是本地局域网（用netmask分别和自己的ip和要访问的ip作与操作看是否相同）不是本地局域网通过网关（GateWay局域网中一般是出口路由器）访问相关的ip    网关中有相应的转发表   网络表记录着网络位对应的网关  通过对应的网关访问其局域网内的相应主机

![image-20200505185242761](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140818.png)

DHCP:动态主机配置协议 属于应用层协议 可以给电脑的mac分配一个ip地址

开机--->电脑尝试连接记录过的wifi--->连入网络 发送广播寻找DHCP服务器--->多个DHCP服务器给电脑ip地址并询问是否使用--->广播确实使用某ip地址 其余的DHCP服务器知道它们分配的ip地址未被使用

DHCP协议

![image-20200505202510333](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140852.png)

- 公网IP：唯一固定 不会有一样的公网IP

- 私网IP：局域网内的ip  不同路由器之间可以出现一样的ip地址


两个不同的私网之间不能通信，必须由公网中转一下

静态ip会让mac地址对应特定的ip地址

socket连接应用层和运输层

私网ip------->公网ip

公网ip怎么发包给私网ip呢？？？？

![image-20200505193350442](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140822.png)

![image-20210404095515900](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210404095524.png)

![image-20200505193645197](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140824.png)

每个路由器都有自己的本地转发表：决定了整个网络范围内端到端的路径选择  路由器是一种特种计算机

虚电路：建立连接  类似早期打电话  需要先打给业务员由业务员转接

![image-20200505194146806](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140834.png)

![image-20200505194524200](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140836.png)



分组网络：

![image-20200505194510585](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140838.png)

路由器结构：工业级的路由器很贵   读取速度 处理速度  底层交换电路材质

- 路由选择处理器   负责更新转发表  不参与决定包要发往哪个路径
- 交换结构   
- 输入输出端口  全双工
- 转发表



![image-20200505194857310](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140841.png)

![image-20200505195255637](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140843.png)

数据链路层头部用于寻找本地的数据传输   数据链路层将数据给物理层 物理层传输数据 按照链路层头部寻找要发送的设备

![image-20200505195803971](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140845.png)

![image-20200505200544798](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140847.png)



ip报文   头部20Bytes

![image-20200505201733513](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140850.png)

- 数据报长度占16位   2<sup>16</sup>-1  65535
- 寿命：经过路由器的个数  跳数



![image-20200505202832002](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140855.png)

数据由公网----->私网     NAT转换表要有记录

LAN局域网/WAN广域网

通过网络地址转换将私网ip转换10.0.0.1，3345------>138.76.29.7，5001，经过多次NAT转换后肯定会转换成公网ip

每个路由器都会有地址转换



ping用的ICMP协议

![image-20200505204207046](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140859.png)

![image-20200505204433736](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140901.png)

![image-20200505204551114](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140904.png)

路径信息协议RIP-----路由选择算法

![image-20200505204950248](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140907.png)

30s会交换邻居间信息   180s无交互邻居已死

![image-20200505205308419](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140909.png)

不仅向邻居 而是向自治系统as中所有的所有路由选择广播

![image-20200505205550750](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140912.png)

边界网关协议BGP  分自治系统内部和外部  通过tcp连接

# 4.链路层

局域网内部

链路层能做可靠交付 但是我们不做 代价太大



![image-20200507180636496](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140914.png)

![image-20200507181524488](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140915.png)

网卡是数据链路层设备  网卡跟总线相连 能够直接访问cpu和内存

差错检测：

- 奇偶校验
- 检验和
- 循环冗余检验

![image-20200507182122864](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140918.png)





## 信道划分协议 TDMA FDMA  CDMA

- 时隙划分
- 频域划分

![image-20200507183606902](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140921.png)

CDMA 蜂窝网络  解决同一个信道传输多个用户信息的问题   手机用的网络就是蜂窝网络

跳频技术  

弹钢琴同时有两首在弹 其实你也是可以分别出两首歌是啥

![image-20200507184259332](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140923.png)

![image-20210806170938530](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210806170948.png)

发送两个bit -1和1  调制 bit * code， 接收端解调

![image-20200507184900825](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140929.png)

两个用户在一个信道上同时发送数据，code不同，解调的code不同 得到不同的数据

cdma之母：海蒂.拉玛

## 随机接入协议 时隙ALOHA

![image-20200507185826569](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140927.png)

考虑在一个时隙内所有的主机都应该能得到发送方的信息 否则信息丢失  所以网络距离不要太远

![image-20200507190011932](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140934.png)

![image-20200507190245238](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140936.png)



## 载波侦听多路访问/碰撞检测CSMA/CD

![image-20200507190519024](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140938.png)

碰撞检测

## 轮流协议 轮询协议

![image-20200507190911854](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140940.png)

master分别给允许信号给slaves   改成举手信号更好  主从机 

master一死，网络瘫痪，单点故障

![image-20200507191039523](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140943.png)

问题：机器无数据要发 但是也要询问   当某个机器拿到令牌之后挂掉了 没了

## 以太网技术   以太网帧结构

![image-20200507191141941](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140947.png)

前同步码唤醒网卡

地址指mac地址

局域网中的设备都会接受到数据 设备判断目的地址是否是自己 是自己就进行数据处理

1500-40=1460    MTU 最大传输单元     1500-IP报文头部-TCP报文头部

IP报文数据长度最大位65535  而以太网帧最大数据长度为1500  所以每次ip包长度为1500就可以（包含tcp/ip头）



## 交换机表

![image-20200507191645893](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210314140950.png)

交换机是根据数据帧中的目的地址决定将包发给谁

路由器是根据目的ip地址决定出口

当设备启动时连入网络需要发送广播信号，255.255.255.255，通过交换机时，将广播报文转发给交换机中的每一个设备，并且自学习接口和mac地址的关系存入交换机表

局域网中通信其实只用mac地址就可以通信 但是mac地址更难记 所以使用ip地址

- 将局域网和广域网看成一个网络 上层更好实现
- ip地址比mac地址好记

通过ip找局域网内的主机：当不知道局域网中的目的ip地址是谁（主机静默很长时间 交换机表没有记录） 广播所有机器 ff-ff-ff-ff-ff-ff  有主机就回复自己的mac地址  （ICMP协议判断是否在此局域网 在局域网中发广播信息寻找这个子网）

家用路由器是简单的路由器和交换机的结合

只有出局域网的数据会经过路由器，局域网内的设备通信通过交换机实现

路由器只会判断ip地址的前n位，判断这个ip地址是不是局域网内的！！！！



# 5.物理层

IEEE 802.3 本地局域网   802.11  无线网

双绞线  5类 超5类 6类 超6类 最慢   距离100米衰减很严重    质量好kM

同轴电缆 

光纤  光衰减小 适合长距离传播   轻松到万M

wireshark 抓包工具  







