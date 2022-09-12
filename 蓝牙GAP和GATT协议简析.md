# 蓝牙GAP和GATT协议简析

## 1、基础简介

1.1、profile

profile 可以理解为一种规范，一个标准的通信协议，它存在于蓝牙从机中（服务端）；

蓝牙组织规定了一些标准的 profile，例如 HID OVER GATT，防丢器，心率计等；

每个 profile 中会包含多个 service，每个 service 代表从机的一种能力。

1.2、service

service 可以理解为一个服务，在 BLE 从机中有多个服务，例如：电量信息服务、系统信息服务等；

每个 service 中又包含多个 characteristic 特征值；

每个具体的 characteristic 特征值才是 BLE 通信的主题，比如当前的电量是 80%，电量的 characteristic 特征值存在从机的 profile 里，这样主机就可以通过这个 characteristic 来读取 80% 这个数据。

1.3、characteristic

characteristic 特征值，BLE 主从机的通信均是通过 characteristic 来实现，可以理解为一个标签，通过这个标签可以获取或者写入想要的内容。

1.4、UUID

UUID，统一识别码，我们刚才提到的 service 和 characteristic 都需要一个唯一的 uuid 来标识；

每个从机都会有一个 profile，不管是自定义的 simpleprofile，还是标准的防丢器 profile，他们都是由一些 service 组成，每个 service 又包含了多个 characteristic，主机和从机之间的通信，均是通过characteristic来实现。

1.5、在BLE中；GATT是焦点；

在链路层（LL），可以把设备分为主机和从机，从机广播，主机发起连接；

在GAP层，可以把设备分为中心设备和外围设备；

在GATT层，可以把设备分为服务端和客户端；

**这些划分是相互之间不影响的**

***

## BLE GATT 简介

低功耗蓝牙 BLE 的连接是建立在 GATT (Generic Attribute Profile) 协议之上的；

GATT 是一个在蓝牙连接之上的发送和接收很短的数据段的通用规范，这些很短的数据段被称为属性（Attribute）。

2.1、GAP 协议

详细介绍 GATT 之前，需要了解 GAP（Generic Access Profile），它在用来控制设备连接和广播；

GAP 使你的设备被其他设备可见，并决定了你的设备是否可以或者怎样与合同设备进行交互；

例如：

Beacon 设备就只是向外广播，不支持连接，小米手环就等设备就可以与中心设备连接。

2.1.1、设备角色

GAP 给设备定义了若干角色，其中主要的两个是：外围设备（Peripheral - 从机 - 服务端）和中心设备（Central - 主机 - 客户端）。

外围设备 - 从机：

这一般就是非常小或者简单的低功耗设备，用来提供数据，并连接到一个更加相对强大的中心设备，例如小米手环；

中心设备 - 主机：

中心设备相对比较强大，用来连接其他外围设备。例如手机等；

2.1.2、广播数据

在 GAP 中外围设备通过两种方式向外广播数据：

Advertising Data Payload（广播数据）和 Scan Response Data Payload（扫描回复）

每种数据最长可以包含 31 byte。这里广播数据是必需的，因为外设必需不停的向外广播，让中心设备知道它的存在；

扫描回复是可选的，中心设备可以向外设请求扫描回复，这里包含一些设备额外的信息，例如：设备的名字。

2.1.3、广播的网络拓扑结构

大部分情况下外设通过广播自己来让中心设备发现自己，并建立 GATT 连接，从而进行更多的数据交换；

也有些情况是不需要连接的，只要外设广播自己的数据即可，用这种方式主要目的是让外围设备，把自己的信息发送给多个中心设备；

因为基于 GATT 连接的方式的，只能是一个外设连接一个中心设备，使用广播这种方式最典型的应用就是苹果的 iBeacon。

2.2、GATT 协议

在说GATT之前先说下ATT;

ATT的Clicent/Server架构：

服务设备提供数据，客户端使用这些数据；服务端通过操作属性的方式，提供数据访问服务设备的服务/客户角色，不依赖与GAP层中心设备/外围设备角色，一个设备可能同时作为一个客户端和服务端，而两个设备伤的属性不会相互影响。![image-20210717100745550](C:\Users\22859\AppData\Roaming\Typora\typora-user-images\image-20210717100745550.png)  

ATT的Attribute Table Example（属性表示例）

Handle：属性在列表中的地址

Type：说明代表什么数据，可以是BluetoothSIG分配或者客户自定义的UUID（统一识别码，具有唯一性和通用性）

Permissions；权限，定义了client是否可以访问属性的值，以及特定的访问方式。

GATT的Client/Server架构：

GATT指定了profile数据交换所在的结构

除了数据的封装方式不同，client/server和Attribute协议结构完全相同，数据封装在“Service”里，用“Characteristic”表示。

GATT的Service中的Characteristic结构和ATT的Attribute协议结构相同![image-20210717102503381](C:\Users\22859\AppData\Roaming\Typora\typora-user-images\image-20210717102503381.png)  



**GATT的profile层次结构**：

为了实现用户的应用，profile通常有一个或者多个"Services"组成。

一个service或许包含某个特征值“Characteristicvalues”（例如，在一个温度采集设备中，通常会包含一个温度的特征值）。

每一个特征值必须由占用一个特征声明结构，其中包括他的其他特性，它是服务端和客户端共享的读写空间，这个特征值可以包含一个可选的描述（descriptor字串），来指示这个特征值的含义。

GATT的Characteristic Declaration（声明）：

Handle 40是一个特征值的声明，用0x2803来指示，这个0x2803同样也是Bluetooth SIG的相关数据手册定义的，作为GATT Characteristic Declaration的UUID

特征值的属性值包含5个字节的长度10：29：00：E1:FF

*0xFFFE1，表明特征值的属类型（0xFFE1：客户自定义特征值的UUID）

*0x0029，是这个值所保存的位置handle（0x0029=41）

*0x10，表明这个特征值的操作权限0x10：notifyonly

**GATT 的Characteristic Configuration：**

另外作为特征值声明，可以有一个可选的描述信息。

这个例子中，handle 42包含了特征值的配置信息，0x2902，这个值同样也是BluetoothSIG的相关数据手册定义的，作为GATT ClientCharacteristic Configuration的UUID。

这个配置值由读写权限，意味着，GATT客户端可以改变这个值（通知开关使能）从0x0000Notificationoff改为0x0001 notification，GATT服务器将开始发送这个特征值的通知到GATT客户端。GATT Service Example：（这个是重点）

**△Handle句柄 ——属性在表中的地址，每个属性有唯一的句柄。**

**△type 类型 ——表示数据代表的事务，通常是蓝牙技术联盟规定的或由用户自定义UUID。**

**△权限 ——对顶了GATT客户端设备对属性的访问权限，包括是否能访问和怎样访问。**

![image-20210717114742825](C:\Users\22859\AppData\Roaming\Typora\typora-user-images\image-20210717114742825.png)

首先我们来看一下GATT属性表中有一些特殊的属性类型，其值是由蓝牙技术联盟（SIG）定义：

**△GATT_PRIMARY_SERVICE_UUID ——表示新服务的起始和提供的服务类型；**

**△GATT_CHARACTER_UUID ——称为“特征声明”紧随其后的是GATT特征值；**

**△GATT_CLIENT_CHAR_CFG_UUID ——这一属性代表特征描述符，它与属性表中它前面最近的特征值有关，他允许 GATT客户端设备使能特征值通知。**

**△GATT_CHAR_USER_DESC_UUID ——这一属性代表特征值描述符，他与属性表中他前面最近的句柄处的特征值相 关，包含一个ASCCI字符串，是对相关的特征的描述**。



在Handle为39的一行中，0x2800表示新服务的起始，profile通常有一个或者多个“Services”组成。
在Handle为40的一行中，这个是特征声明；它的特征值的属性值包含5个字节的长度10：29：00：E1：FF

*0xFFE1，表明特征值的属类型（0xFFE1：客户自定义特征值的UUID）

*0x0029，是这个值所保存的位置handle（0x0029=41）

*0x10，表明这个特征值的操作权限0x10：notifyonly

个人感觉这个地方类似于C语言中的变量的定义：这个一行就相当于定义了一个整型变量a；

int a；0xFFE1就相当于a；0x0029类似于a在内存中的地址；

在Handle为41的一行中，其特征值就相当于a的值；这也是用户自定义的有效数据；

在Handle为42的一行中，这个是特征描述符， 



在Handle为43的一行中，0x2800表示又一个新服务的起始


GATT 的全名是 Generic Attribute Profile，它定义两个 BLE 设备通过 Service 和 Characteristic 进行通信；

GATT 使用了 ATT（Attribute Protocol）协议，ATT 协议把 Service, Characteristic 对应的数据保存在一个查找表中，查找表使用 16bit ID 作为每一项的索引；

一旦两个设备建立起了连接，GATT 就开始起作用了，这也意味着，你必需完成前面的 GAP 协议；

这里需要说明的是，GATT 连接必需先经过 GAP 协议，实际上，我们在 Android 开发中，可以直接使用设备的 MAC 地址，发起连接，可以不经过扫描的步骤；

这并不意味不需要经过 GAP，实际上在芯片级别已经给你做好了，蓝牙芯片发起连接，总是先扫描设备，扫描到了才会发起连接；

GATT 连接需要特别注意的是：GATT 连接是独占的。也就是一个 BLE 外设同时只能被一个中心设备连接；

一旦外设被连接，它就会马上停止广播，这样它就对其他设备不可见了，当设备断开，它又开始广播；

中心设备和外设需要双向通信的话，唯一的方式就是建立 GATT 连接。

2.2.1、GATT 连接的网络拓扑

一个外设只能连接一个中心设备，而一个中心设备可以连接多个外设，ConnectedTopology 一旦建立起了连接，通信就是双向的了，对比前面的 GAP 广播的网络拓扑，GAP 通信是单向的，如果你要让两个设备外设能通信，就只能通过中心设备中转。

2.2.2、GATT 通信事务

GATT 通信的双方是 C/S 关系，外设作为 GATT 服务端（Server），它维持了 ATT 的查找表以及 service 和 characteristic 的定义；

中心设备是 GATT 客户端（Client），它向 Server 发起请求，需要注意的是，所有的通信事件，都是由客户端（也叫主设备，Master）发起，并且接收服务端（也叫从设备，Slave）的响应；

一旦连接建立，外设将会给中心设备建议一个连接间隔（Connection Interval）,这样中心设备就会在每个连接间隔尝试去重新连接，检查是否有新的数据；

但是，这个连接间隔只是一个建议，你的中心设备可能并不会严格按照这个间隔来执行，例如你的中心设备正在忙于连接其他的外设，或者中心设备资源太忙；

2.2.3、GATT 结构

GATT 事务是建立在嵌套的Profiles, Services 和 Characteristics之上的的。

Profile 并不是实际存在于 BLE 外设上的，它只是一个被 Bluetooth SIG 或者外设设计者预先定义的 Service 的集合；

例如心率 Profile（Heart Rate Profile）就是结合了 Heart Rate Service 和 Device Information Service；

Service 是把数据分成一个个的独立逻辑项，它包含一个或者多个 Characteristic，每个 Service 有一个 UUID 唯一标识，UUID 有 16bit 的，或者 128bit 的，16bit 的 UUID 是官方通过认证的，需要花钱购买，128 bit 是自定义的，这个就可以自己随便设置；

以 Heart Rate Service 为例，可以看到它的官方通过 16bit UUID 是 0x180D，包含 3 个 Characteristic：Heart Rate Measurement, Body Sensor Location 和 Heart Rate Control Point，并且定义了只有第一个是必须的，它是可选实现的；

Characteristic在 GATT 事务中的最低界别的是 Characteristic，Characteristic 是最小的逻辑数据单元，当然它可能包含一个组关联的数据，例如加速度计的 X/Y/Z 三轴值。

与 Service 类似，每个 Characteristic 用 16bit 或者 128bit 的 UUID 唯一标识，你可以免费使用 Bluetooth SIG 官方定义的标准 Characteristic，使用官方定义的，可以确保 BLE 的软件和硬件能相互理解；

举个例子：

Heart Rate Measurement Characteristic，这是上面提到的 Heart Rate Service 必需实现的 Characteristic，它的 UUID 是 0x2A37。它的数据结构是，开始 8 bit 定义心率数据格式，接下来就是对应格式的实际心率数据；

和 BLE 外设打交道，主要是通过 Characteristic。你可以从 Characteristic 读取数据，也可以往 Characteristic 写数据，这样就实现了双向的通信。所以你可以自己实现一个类似串口（UART）的 Sevice，这个 Service 中包含两个 Characteristic，一个被配置只读的通道（RX），另一个配置为只写的通道（TX）。
