# 蓝牙学习BLE篇

​	**蓝牙ble的传输速率是指主从机每秒所传输的字节数。既然是传输速率那就涉及到时间和每次锁传递包大小的问题。**

ble对于数据的**传输有一个字节上的限制**，默认情况下是20个字节，但并不是不可修改的。默认情况下mtu是23个字节（除去3个字节的标志位剩余为20个字节），主机完全可以通过调用BluetooGatt#requestMtu（int mtu）来修改每个包锁传输的字节数。

同样，ble在属于传输时对于每个包之间的时间间隔也有一定的限制，大多数从机的连接时间间隔为7.5ms–4s（以1.25ms为一个单位，也就是6-0x0C80个单位）。

****

### **BLE协议分层**：

![image-20210722154932304](C:\Users\22859\AppData\Roaming\Typora\typora-user-images\image-20210722154932304.png)

一个蓝牙工程应用被分成三个层，分别为应用层，主协议层，控制层。

**BLE的各个概念的意思：**

**PHY** **层**（Physical layer 物理层）。PHY 层用来指定 BLE 所用的无线频段，调制解调方 式和方法等。PHY 层做得好不好，直接决定整个 BLE 芯片的功耗，灵敏度以及 selectivity 等 射频指标。 

**LL** **层**：（Link Layer 链路层）。LL 层是整个 BLE 协议栈的核心，也是 BLE 协议栈的难点和 重点。像 Nordic 的 BLE 协议栈能同时支持 20 个 link（连接），就是 LL 层的功劳。LL 层要 做的事情非常多，比如具体选择哪个射频通道进行通信，怎么识别空中数据包，具体在哪个时 间点把数据包发送出去，怎么保证数据的完整性，ACK 如何接收，如何进行重传，以及如何对 链路进行管理和控制等等。LL 层只负责把数据发出去或者收回来，对数据进行怎样的解析则 交给上面的 GAP 或者 ATT。 

**HCI**：（Host controller interface）。HCI 是可选的，HCI 主要用于 2 颗芯片实现 BLE 协 议栈的场合，用来规范两者之间的通信协议和通信命令等。 

**GAP** **层**（Generic access profile）。GAP 是对 LL 层 payload（有效数据包）如何进行解析的两种方式中的一种，而且是最简单的那一种。GAP 简单的对 LL payload 进行一些规范和定义，因此 GAP 能实现的功能极其有限。GAP 目前主要用来进行广播，扫描和发起连接等。 

**L2CAP** **层**（Logic link control and adaptation protocol）。L2CAP 对 LL 进行了一次 简单封装，LL 只关心传输的数据本身，L2CAP 就要区分是加密通道还是普通通道，同时还要 对连接间隔进行管理。 

**SMP**（Secure manager protocol）。SMP 用来管理 BLE 连接的加密和安全的，如何保 证连接的安全性，同时不影响用户的体验，这些都是 SMP 要考虑的工作。 

**ATT**（Attribute protocol）。简单来说，ATT 层用来定义用户命令及命令操作的数据，比如读取某个数据或者写某个数据。BLE 协议栈中，开发者接触最多的就是 ATT。**BLE** **引入了** **attribute** **概念，用来描述一条一条的数据**。Attribute 除了定义数据，同时定义该数据可以 使用的 ATT 命令，因此这一层被称为 ATT 层。 

**GATT**（Generic attribute profile ）。GATT 用来规范 attribute 中的数据内容，并运 用 group（分组）的概念对 attribute 进行分类管理。没有 GATT，BLE 协议栈也能跑，但互 联互通就会出问题，也正是因为有了 GATT 和各种各样的应用 profile，BLE 摆脱了 ZigBee 等无线协议的兼容性困境，成了出货量最大的 2.4G 无线通信产品。 最上层的 Profiles 层里，包含的公用任务和私有任务，其中公共任务是 SIG 蓝牙协议小组 定义的蓝牙任务，私有任务是用户或者企业自定义的蓝牙任务。

* 主机和从机：发起连接的设备是主机，接收连接请求的设备是从机
* 客户端和服务器：展示“属性”的设备是服务器，与之配对的是客户端。换言之，获取信息的是客户端，提供信息的是服务器
* 主机可以是客户端也可以是服务器端，同样从机可以做客户端也可以做服务器端
* BLE协议和协议栈：协议指的是一种通信标准。比如规定第一个字节代表什么意思第二个字节又代表什么意思。而协议栈是具体的一些实现函数，开发人员调用协议栈函数进行通信（相当于各种API）
* 连接事件：在两个ble设备的连接中使用调频机制，两个设备使用特定的信道收发数据，过段时间再使用新的信道（链路层处理信道切换），两个设备在信道切换后收发数据称为连接事件。即使没有数据收发，两设备仍旧会交换链路层数据来维持连接，在一次连接事件中会不停的切换信道，所发送的数据包不止一个。
* 连接间隔：就是两个连接事件之间的间隔。主机开始发送数据到从机至下一次主机开始发送数据到从机之间的时间间隔。主从机之间的每次通信都是一个连接事件（但不同于第一次从机处于广播状态下主机连接从机）。以1.25ms为一个单位，一般取值是7.5ms-4s（6-3200）.
* 从机延时：允许从机跳过一些连接事件。简单说就是我规定一个时间，在这次连接事件开始到一定时间内，在这一段时间内从机不响应主机的任何消息。
* 监控超时：两个成功连接事件间的最大允许间隔。如果超过了这个时间而没有任何连接事件即没有任何数据交换则断开连接。以10ms为一个单位，一般取值范围是100ms-32s（10-3200）.

***

主机在发起连接之后会获取一个BluetoothGatt对象。

* 主机主动读数据调用readcharacteristic方法，读之后会触发onCharacteristicRead方法
* 主机主动写数据调用writeCharacteristic方法，写之后会触发onCharacteristicWrite方法（可以进行下一次的写数据）
* 主机被动获取到数据（从机通过notify方法发送数据），当监听到有数据过来时会触发onCharacteristicChange方法（但是要想该方法回调，必须在主机端程序中为要监听的characteristic设置notify： setCharacteristicNotification）
* 主机修改mtu（一个包的字节）会触发onMtuChanged方法。

***

主机从机连接通信过程描述：

* 主机开启蓝牙enable（）；

* 主机扫描蓝牙startLeScan（）（startLeScan(final UUID[]serviceUUids,finalLeScanCallbacke callback)可以扫描特定uuid的设备）

* 主机connect连接蓝牙

* 主机discoveryService发现服务（会回调onServicesDiscovered方法）

* 进行通信


***

# ble相关api

​	**BLE**：Bluetooth Low Energy，即蓝牙低功耗，它是一种技术，从蓝牙4.0开始支持。蓝牙低功耗芯片分为两种模式：单模和双模

* 单模：只能执行低功耗协议栈，也就是只支持ble
* 双模：支持传统蓝牙以及ble使用

较传统蓝牙：传输速度更快，覆盖范围更广，安全性更高，延迟更短，耗电更低等优点。

**关键术语和概念**：

Gatt：(Generic Attribute Profile)—通用属性配置文件，用于在ble链路上发送和接收被称为“属性”的数据块。目前所有的ble应用都是基于GATT的。一个设备可以实现多个配置文件。

ble交互的桥梁是**Service、Characteristic、Desciptor。**

Characteristic：可以理解为一个数据类型，它包括一个value和0至多个对此characteristic的描述（Descriptor）。

Descriptor：对Characterisctic的描述，如范围、单位等。

Service：Characteristic的集合。它可以包含多个Characteristic。

一个ble终端可以包含多个Service，一个Service可以包含多个Characteristic，一个Characteristic包含一个value和多个Descriptor，一个Descriptor包含一个value。其中Characteristic比较重要，用的比较多。
这三部分都由UUID作为唯一标示符，以此区分。
UUID（Universally Unique Identifier），含义是通用唯一识别码，它是在一定范围内唯一的机器生成的标识符。标准的UUID格式为：xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (8-4-4-4-12)。

ble中有四个**角色**：

广播者（Braodcaster）：广播发送者，是不可连接的设备。

观察者（Observer）：扫描广播，不能够启动连接。

广播者和观察者不能建立连接。应用：温度传感器和温度显示器。

外围（periphery）：广播发送者，可连接的设备，在单一链路层作为从机。

中央（central）：扫描广播，启动连接，在单一或多链路层作为主机。

中央和外围可以进行配对、连接、数据通信。应用：手机和手表。

一个中央可以同时连接多个周边，但是一个周边只能连接一个中央(但是我测试，周边可以连接多个中央设备，并且能正常通信)。

