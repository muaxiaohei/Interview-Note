# MQTT协议

## 1 简述

MQTT（Message Queuing Telemetry Transport，消息队列遥测传输协议），是一种基于发布/订阅（publish/subscribe）模式的"轻量级"通讯协议，该协议构建于TCP/IP协议上，由IBM在1999年发布。MQTT最大优点在于，可以以极少的代码和有限的带宽，为连接远程设备提供实时可靠的消息服务。作为一种低开销、低带宽占用的即时通讯协议，使其在物联网、小型设备、移动应用等方面有较广泛的应用。

MQTT是一个基于客户端-服务器的消息发布/订阅传输协议。MQTT协议是轻量、简单、开放和易于实现的，这些特点使它适用范围非常广泛。在很多情况下，包括受限的环境中，如：机器与机器（M2M）通信和物联网（IoT）。其在，通过卫星链路通信传感器、偶尔拨号的医疗设备、智能家居、及一些小型化设备中已广泛使用。

<img src="https://static.runoob.com/images/mix/mqtt-fidge-2.svg" alt="img" style="zoom: 67%;" />

### 1.1 设计规范

由于物联网的环境是非常特别的，所以MQTT遵循以下设计原则：

- （1）精简，不添加可有可无的功能；
- （2）发布/订阅（Pub/Sub）模式，方便消息在传感器之间传递；
- （3）允许用户动态创建主题，零运维成本；
- （4）把传输量降到最低以提高传输效率；
- （5）把低带宽、高延迟、不稳定的网络等因素考虑在内；
- （6）支持连续的会话控制；
- （7）理解客户端计算能力可能很低；
- （8）提供服务质量管理；
- （9）假设数据不可知，不强求传输数据的类型与格式，保持灵活性。


### 1.2 主要特性

MQTT协议工作在低带宽、不可靠的网络的远程传感器和控制设备通讯而设计的协议，它具有以下主要的几项特性：

- （1）使用发布/订阅消息模式，提供一对多的消息发布，解除应用程序耦合。

  这一点很类似于XMPP，但是MQTT的信息冗余远小于XMPP，,因为XMPP使用XML格式文本来传递数据。

- （2）对负载内容屏蔽的消息传输。

- （3）使用TCP/IP提供网络连接。

  主流的MQTT是基于TCP连接进行数据推送的，但是同样有基于UDP的版本，叫做MQTT-SN。这两种版本由于基于不同的连接方式，优缺点自然也就各有不同了。

- （4）有三种消息发布服务质量：

  "至多一次"，消息发布完全依赖底层TCP/IP网络。会发生消息丢失或重复。这一级别可用于如下情况，环境传感器数据，丢失一次读记录无所谓，因为不久后还会有第二次发送。这一种方式主要普通APP的推送，倘若你的智能设备在消息推送时未联网，推送过去没收到，再次联网也就收不到了。

  "至少一次"，确保消息到达，但消息重复可能会发生。

  "只有一次"，确保消息到达一次。在一些要求比较严格的计费系统中，可以使用此级别。在计费系统中，消息重复或丢失会导致不正确的结果。这种最高质量的消息发布服务还可以用于即时通讯类的APP的推送，确保用户收到且只会收到一次。

- （5）小型传输，开销很小（固定长度的头部是2字节），协议交换最小化，以降低网络流量。

  这就是为什么在介绍里说它非常适合"在物联网领域，传感器与服务器的通信，信息的收集"，要知道嵌入式设备的运算能力和带宽都相对薄弱，使用这种协议来传递消息再适合不过了。

- （6）使用Last Will和Testament特性通知有关各方客户端异常中断的机制。

  Last Will：即遗言机制，用于通知同一主题下的其他设备发送遗言的设备已经断开了连接。

  Testament：遗嘱机制，功能类似于Last Will。

------

### 1.3 MQTT协议原理

#### 1.3.1 MQTT协议实现方式

实现MQTT协议需要客户端和服务器端通讯完成，在通讯过程中，MQTT协议中有三种身份：发布者（Publish）、代理（Broker）（服务器）、订阅者（Subscribe）。其中，消息的发布者和订阅者都是客户端，消息代理是服务器，消息发布者可以同时是订阅者。

MQTT传输的消息分为：主题（Topic）和负载（payload）两部分：

- （1）Topic，可以理解为消息的类型，订阅者订阅（Subscribe）后，就会收到该主题的消息内容（payload）；
- （2）payload，可以理解为消息的内容，是指订阅者具体要使用的内容。

#### 1.3.2 网络传输与应用消息

MQTT会构建底层网络传输：它将建立客户端到服务器的连接，提供两者之间的一个有序的、无损的、基于字节流的双向传输。

当应用数据通过MQTT网络发送时，MQTT会把与之相关的服务质量（QoS）和主题名（Topic）相关连。

#### 1.3.3 MQTT客户端

一个使用MQTT协议的应用程序或者设备，它总是建立到服务器的网络连接。客户端可以：

- （1）发布其他客户端可能会订阅的信息；
- （2）订阅其它客户端发布的消息；
- （3）退订或删除应用程序的消息；
- （4）断开与服务器连接。

#### 1.3.4 MQTT服务器

MQTT服务器以称为"消息代理"（Broker），可以是一个应用程序或一台设备。它是位于消息发布者和订阅者之间，它可以：

- （1）接受来自客户的网络连接；
- （2）接受客户发布的应用信息；
- （3）处理来自客户端的订阅和退订请求；
- （4）向订阅的客户转发应用程序消息。

#### 1.3.5 MQTT协议中的订阅、主题、会话

**一、订阅（Subscription）**

订阅包含主题筛选器（Topic Filter）和最大服务质量（QoS）。订阅会与一个会话（Session）关联。一个会话可以包含多个订阅。每一个会话中的每个订阅都有一个不同的主题筛选器。

**二、会话（Session）**

每个客户端与服务器建立连接后就是一个会话，客户端和服务器之间有状态交互。会话存在于一个网络之间，也可能在客户端和服务器之间跨越多个连续的网络连接。

**三、主题名（Topic Name）**

连接到一个应用程序消息的标签，该标签与服务器的订阅相匹配。服务器会将消息发送给订阅所匹配标签的每个客户端。

**四、主题筛选器（Topic Filter）**

一个对主题名通配符筛选器，在订阅表达式中使用，表示订阅所匹配到的多个主题。

**五、负载（Payload）**

消息订阅者所具体接收的内容。

#### 1.3.6 MQTT协议中的方法

MQTT协议中定义了一些方法（也被称为动作），来于表示对确定资源所进行操作。这个资源可以代表预先存在的数据或动态生成数据，这取决于服务器的实现。通常来说，资源指服务器上的文件或输出。主要方法有：

- （1）Connect。等待与服务器建立连接。
- （2）Disconnect。等待MQTT客户端完成所做的工作，并与服务器断开TCP/IP会话。
- （3）Subscribe。等待完成订阅。
- （4）UnSubscribe。等待服务器取消客户端的一个或多个topics订阅。
- （5）Publish。MQTT客户端发送消息请求，发送完成后返回应用程序线程。



## 2 MQTT控制报文格式

在MQTT协议中，一个MQTT数据包由：**固定头（Fixed header）、可变头（Variable header）、消息体（payload）三部分构成**。MQTT数据包结构如下：

- （1）固定头（Fixed header）。存在于所有MQTT数据包中，表示数据包类型及数据包的分组类标识。
- （2）可变头（Variable header）。存在于部分MQTT数据包中，数据包类型决定了可变头是否存在及其具体内容。
- （3）消息体（Payload）。存在于部分MQTT数据包中，表示客户端收到的具体内容。

整体MQTT的消息格式如下图所示：

![img](.\MQTT.assets\mqtt数据包结构.png)

### 2.1 MQTT固定头 （Fixed header）

固定头存在于所有MQTT数据包中，其结构如下：

![img](.\MQTT.assets\mqtt固定头.png)

#### 2.1.1 MQTT数据包类型

位置：Byte 1中bits 7-4。

相于一个4位的无符号值，类型、取值及描述如下：

**表格 2.1 -控制报文的类型**

| 名字        | 值   | 报文流动方向   | 描述                                |
| ----------- | ---- | -------------- | ----------------------------------- |
| Reserved    | 0    | 禁止           | 保留                                |
| CONNECT     | 1    | 客户端到服务端 | 客户端请求连接服务端                |
| CONNACK     | 2    | 服务端到客户端 | 连接报文确认                        |
| PUBLISH     | 3    | 两个方向都允许 | 发布消息                            |
| PUBACK      | 4    | 两个方向都允许 | QoS 1消息发布收到确认               |
| PUBREC      | 5    | 两个方向都允许 | 发布收到（保证交付第一步）          |
| PUBREL      | 6    | 两个方向都允许 | 发布释放（保证交付第二步）          |
| PUBCOMP     | 7    | 两个方向都允许 | QoS 2消息发布完成（保证交互第三步） |
| SUBSCRIBE   | 8    | 客户端到服务端 | 客户端订阅请求                      |
| SUBACK      | 9    | 服务端到客户端 | 订阅请求报文确认                    |
| UNSUBSCRIBE | 10   | 客户端到服务端 | 客户端取消订阅请求                  |
| UNSUBACK    | 11   | 服务端到客户端 | 取消订阅报文确认                    |
| PINGREQ     | 12   | 客户端到服务端 | 心跳请求                            |
| PINGRESP    | 13   | 服务端到客户端 | 心跳响应                            |
| DISCONNECT  | 14   | 客户端到服务端 | 客户端断开连接                      |
| Reserved    | 15   | 禁止           | 保留                                |

#### 2.1.2 标识位（Flags）

固定报头第1个字节的剩余的4位 [3-0]包含每个MQTT控制报文类型特定的标志。表格 2.2中任何标记为“保留”的标志位，都是保留给以后使用的，**必须**设置为表格中列出的值 [MQTT-2.2.2-1]。如果收到非法的标志，接收者**必须**关闭网络连接。有关错误处理的详细信息见 4.8节 [MQTT-2.2.2-2]。

位置：Byte 1中bits 3-0。

<strong style="color:#00b0f0;">在不使用标识位的消息类型中，标识位被作为保留位。如果收到无效的标志时，接收端必须关闭网络连接：</strong>

**表格 2.2 - 标志位 Flag Bits**

| 控制报文    | 固定报头标志       | Bit 3           | Bit 2           | Bit 1           | Bit 0              |
| ----------- | ------------------ | --------------- | --------------- | --------------- | ------------------ |
| CONNECT     | Reserved           | 0               | 0               | 0               | 0                  |
| CONNACK     | Reserved           | 0               | 0               | 0               | 0                  |
| PUBLISH     | Used in MQTT 3.1.1 | DUP<sup>1</sup> | QoS<sup>2</sup> | QoS<sup>2</sup> | RETAIN<sup>3</sup> |
| PUBACK      | Reserved           | 0               | 0               | 0               | 0                  |
| PUBREC      | Reserved           | 0               | 0               | 0               | 0                  |
| PUBREL      | Reserved           | 0               | 0               | 1               | 0                  |
| PUBCOMP     | Reserved           | 0               | 0               | 0               | 0                  |
| SUBSCRIBE   | Reserved           | 0               | 0               | 1               | 0                  |
| SUBACK      | Reserved           | 0               | 0               | 0               | 0                  |
| UNSUBSCRIBE | Reserved           | 0               | 0               | 1               | 0                  |
| UNSUBACK    | Reserved           | 0               | 0               | 0               | 0                  |
| PINGREQ     | Reserved           | 0               | 0               | 0               | 0                  |
| PINGRESP    | Reserved           | 0               | 0               | 0               | 0                  |
| DISCONNECT  | Reserved           | 0               | 0               | 0               | 0                  |

（1）DUP：发布消息的副本。用来在保证消息的可靠传输，如果设置为1，则在下面的变长中增加MessageId，并且需要回复确认，以保证消息传输完成，但不能用于检测消息重复发送。【控制报文的重复分发标志】

（2）QoS：发布消息的服务质量，即：保证消息传递的次数【PUBLISH报文的服务质量等级】

```
Ø00：最多一次，即：<=1

Ø01：至少一次，即：>=1

Ø10：一次，即：=1

Ø11：预留
```

**QoS 0**

这一级别会发生消息丢失或重复，消息发布依赖于底层TCP/IP网络。即：<=1

![img](.\MQTT.assets\QoS0.png)

**QoS 1**

QoS 1 承诺消息将至少传送一次给订阅者。

![img](.\MQTT.assets\Qos1.png)

**QoS 2**

使用 QoS 2，我们保证消息仅传送到目的地一次。为此，带有唯一消息 ID 的消息会存储两次，首先来自发送者，然后是接收者。QoS 级别 2 在网络中具有最高的开销，因为在发送方和接收方之间需要两个流。

![img](.\MQTT.assets\Qos2.png)

（3）RETAIN： 发布保留标识，表示服务器要保留这次推送的信息，如果有新的订阅者出现，就把这消息推送给它，如果设有那么推送至当前订阅者后释放。【PUBLISH报文的保留标志】

### 2.1.3 剩余长度（Remaining Length）

地址：Byte 2（从第2个字节开始）。

固定头的第二字节用来保存变长头部和消息体的总大小的，但不是直接保存的。这一字节是可以扩展，其保存机制，前7位用于保存长度，后一部用做标识。当最后一位为1时，表示长度不足，需要使用二个字节继续保存。例如：计算出后面的大小为0

剩余长度（Remaining Length）表示当前报文剩余部分的字节数，包括可变报头和负载的数据。剩余长度不包括用于编码剩余长度字段本身的字节数。

剩余长度字段使用一个变长度编码方案，对小于128的值它使用单字节编码。更大的值按下面的方式处理。低7位有效位用于编码数据，最高有效位用于指示是否有更多的字节。因此每个字节可以编码128个数值和一个*延续位（continuation bit）*。剩余长度字段最大4个字节。

> **非规范评注**
>
> 例如，十进制数64会被编码为一个字节，数值是64，十六进制表示为0x40,。十进制数字321(=65+2*128)被编码为两个字节，最低有效位在前。第一个字节是 65+128=193。注意最高位为1表示后面至少还有一个字节。第二个字节是2。
>
> **非规范评注**
>
> 这允许应用发送最大256MB(268,435,455)大小的控制报文。这个数值在报文中的表示是：0xFF,0xFF,0xFF,0x7F。
>
> [表格 2.4剩余长度字段的大小]展示了剩余长度字段所表示的值随字节增长。

##### 表格 2.4剩余长度字段的大小 Size of Remaining Length field

| **字节数** | **最小值**                         | **最大值**                           |
| ---------- | ---------------------------------- | ------------------------------------ |
| 1          | 0 (0x00)                           | 127 (0x7F)                           |
| 2          | 128 (0x80, 0x01)                   | 16 383 (0xFF, 0x7F)                  |
| 3          | 16 384 (0x80, 0x80, 0x01)          | 2 097 151 (0xFF, 0xFF, 0x7F)         |
| 4          | 2 097 152 (0x80, 0x80, 0x80, 0x01) | 268 435 455 (0xFF, 0xFF, 0xFF, 0x7F) |

分别表示（每个字节的低7位用于编码数据，最高位是标志位）：

- 1个字节时，从0(0x00)到127(0x7f)
- 2个字节时，从128(0x80,0x01)到16383(0Xff,0x7f)
- 3个字节时，从16384(0x80,0x80,0x01)到2097151(0xFF,0xFF,0x7F)
- 4个字节时，从2097152(0x80,0x80,0x80,0x01)到268435455(0xFF,0xFF,0xFF,0x7F)

### 2.2 可变报头

MQTT数据包中包含一个可变头，它驻位于固定的头和负载之间。很多控制报文的可变报头部分包含一个两字节的报文标识符字段。这些报文是PUBLISH（QoS > 0时）， PUBACK，PUBREC，PUBREL，PUBCOMP，SUBSCRIBE, SUBACK，UNSUBSCRIBE，UNSUBACK。

##### 图例 2.3 -报文标识符字节 Packet Identifier bytes

| **Bit** | **7** - **0**  |
| :------ | -------------- |
| byte 1  | 报文标识符 MSB |
| byte 2  | 报文标识符 LSB |

### 2.3 Payload消息体

Payload消息体位MQTT数据包的第三部分，包含CONNECT、SUBSCRIBE、SUBACK、UNSUBSCRIBE四种类型的消息：

- （1）CONNECT，消息体内容主要是：客户端的ClientID、订阅的Topic、Message以及用户名和密码。
- （2）SUBSCRIBE，消息体内容是一系列的要订阅的主题以及QoS。
- （3）SUBACK，消息体内容是服务器对于SUBSCRIBE所申请的主题及QoS进行确认和回复。
- （4）UNSUBSCRIBE，消息体内容是要订阅的主题。
- （5）PUBLISH，可选

介绍完基础理论部分，下面在Windows平台上搭建一个简单的MQTT应用，进行简单的应用，整体架构如下图所示；

[外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-ScRucIVO-1625480723109)(架构图.png)]



## 3 环境搭建

介绍完基础理论部分，下面在Windows平台上搭建一个简单的MQTT应用，进行简单的应用，整体架构如下图所示；

![img](.\MQTT.assets\watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTA2MzIxNjU=,size_16,color_FFFFFF,t_70.png)

### 3.1 MQTT服务器搭建

目前MQTT代理的主流平台有下面几个：

- Mosquitto：[https://mosquitto.org/](https://link.zhihu.com/?target=https%3A//mosquitto.org/)
- VerneMQ：[https://vernemq.com/](https://link.zhihu.com/?target=https%3A//vernemq.com/)
- EMQTT：[http://emqtt.io/](https://link.zhihu.com/?target=http%3A//emqtt.io/)

本文将使用 Mosquitoo 进行测试，进入到安装页面，下载自己电脑的系统所适配的程序；

![img](.\MQTT.assets\v2-32a9b4fdc073394c2a28e92e92227cee_1440w.webp)

安装成功之后，进入到安装路径下，找到`mosquitto.exe`；

![img](.\MQTT.assets\下载页面.png)

按住`Shift`，右键鼠标点击空白处，然后打开Powershell，正常打开一个终端软件即可；

- 输入`./mosquitto.exe -h` 可以查看相应的帮助；
- 输入`./mosquitto.exe -p 10086`，就开启了MQTT服务，监听的地址是`127.0.0.1`，端口是`10086`；



### 3.2 MQTT Client

服务器搭建好了，下面就是开启客户端，进行发布和订阅，这样就可以传输相应的消息。

这里我使用的是自己编译了一个`QT mqtt client` 程序，是基于Qt的官方库进行编译的，下面打开这个软件，下一期简单介绍一下如何完成这个客户端，并设置好相应参数：

- 地址：`127.0.0.1`
- 端口：`10086`

然后订阅主题，就可以互相发送数据了，具体如下图所示；

![img](.\MQTT.assets\mqtt客户端1.webp)

结合前面的图片来看，整体的架构如下所示；

![img](.\MQTT.assets\整体架构.png)
