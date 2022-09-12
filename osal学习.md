# osal学习 

​	OSAL全称为：Operating System Avstraction Layer,即操作系统抽象层，支持多任务运行，它并不是传统意义上的操作系统，但是实现了部分类似操作系统的功能。[osal蓝牙例子详解](https://blog.csdn.net/itas109/article/details/12952759?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522162665571916780255225841%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=162665571916780255225841&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~blog~sobaiduend~default-2-12952759.pc_v2_rank_blog_default&utm_term=osal%E4%BA%8B%E4%BB%B6%E9%A9%B1%E5%8A%A8%E6%96%B9%E5%BC%8F&spm=1018.2226.3001.4450)

***

​     OSAL概念是由TI公司在ZIGBEE协议栈引入，他的意思是“模拟操作系统”，此OS，并非一个真正的OS，而是模拟OS的一些方法为广大编程者提供一种写MUC程序的方法。**当有一个事件发生的时候，OSAL负责将此事件分配给能够处理此事件的任务，然后此任务判断事件的类型，调用相应的事件处理程序进行处理。**

***

**Osal主要提供如下功能**：任务注册、任务间同步互斥、中断处理、存储器分配和管理、提供定时器功能

![image-20210719091526341](C:\Users\22859\AppData\Roaming\Typora\typora-user-images\image-20210719091526341.png)

***

###  **OSAL的调度机制**

协议栈调度程序（OSAL）的调度机制分为三部分：1、任务调度；2、时间管理；3、原语通信。

   1、任务调度：osal 采用一个链表结构来管理协议栈各层相应任务。相关操作函数有，添加任务到链表中；获取下一个活动任务；根据taskID查找下一个任务。osal 采用轮询任务调度队列（任务链表），通过两个函数：调度程序主循环函数和设置事件发生标志函数。

   2、时间管理：通过为事件设置超时等待时间，一旦等待时间结束，便为对应任务设置事件发生标志，从而达到对事件进行延时处理目的。

   3、原语通信：请求响应原语操作：一旦调用了下层相关函数后，就立即返回。下层处理函数在操作结束后，将结果以消息的形式发送到上层并产生一个系统事件，调度程序发现这个事件后就会调用相应的事件处理函数对它进行处理。两个相关函数：向目标任务发送消息的函数；消息提取函数。

***

### OSAL管理的实现

​	如果实现软件和硬件的低耦合，使软件不经改动或很少改动就可以应用在另外的硬件上，这样就方便硬件改造、升级、迁移后，软件移植。HAL硬件抽象层正是用来抽象各种硬件的资源，告知给软件。其作用类似于嵌入式系统设备驱动的定义硬件资源的h头文件。

BLE低功耗蓝牙系统架构:

![img](https://img-blog.csdnimg.cn/20190602191219996.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDIxNjA2NQ==,size_16,color_FFFFFF,t_70)

软件功能是由任务事件来实现的，创建一个人物事件需要以下工作；

1、创建 task identifier任务ID；

2、编写任务初始化（task initialization routine）进程，并需要添加到OSAL初始化进程中，这就是说系统启动后不能动态添加功能；

3、编写任务处理程序；

4、如有需要提供消息服务。BLE协议栈的各层都是以OSAL任务方式实现，由于LL控制室的任务要求最为迫切，所以其任务优先级最高。为了实现任务管理，OSAL通过消息处理，存储管理，计时器定时等附加服务实现。

***

### OSAL的系统启动流程

![img](https://img-blog.csdnimg.cn/2019060219171625.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDIxNjA2NQ==,size_16,color_FFFFFF,t_70)

​	为了使用OSAL，在main函数的最后要启动一个名叫osal_start_system的进程，该进程会调用由特定应用决定的启动函数osallnitTasks（来启动系统）。osallnitTasks逐个逐个调用BLE协议栈各层的启动进程来初始化协议栈。随后，设置一个任务的8bit任务id（task ID），跳入循环等待执行任务，系统启动完成。

1. 任务优先级决定于任务ID，任务ID越小，优先级越高；

2. BLE协议栈各层的任务优先级比应用程序高；

3. 初始化协议栈后，越早调入的任务，任务ID越高，优先级越低，即系统倾向于处理新到的任务；

   每个事件任务由对应的16bit事件变量来标示，事件状态由旗号（taskflag）来标示。如果事件处理程序已经完成，但其旗号并没有移除，OSAL会认为事情还没有完成而继续在该程序中不返回。比如，比如，在SimpleBLEPeripheral实例工程中，当事件START_DEVICE_EVT发生，其处理函数SimpleBLEPeripheral_ProcessEvent就运行，结束后返回16bit事件变量，并清除旗语SBP_START_DEVICE_EVT。每当OSAL事件检测到了有任务事件，其相应的处理进程将被添加到由处理进程指针构成的事件处理表单中，该表单名叫taskArr（taskarray）。taskArr 中各个事件进程的顺序和 osalInitTasks 初始化函数中任务ID的顺序是对应的。有两种，最简单的方法是使用osal_set_event函数（函数原型在OSAL.h文件中），在这个函数中，用户可以像定义函数参数一样设置任务ID和事件旗语。第二种方法是使用osal_start_timerEx函数（函数原型在OSAL_Timers.h文件中），使用方法同osal_set_event函数，而第三个以毫秒为单位的参数 osal_start_timerEx 则指示该事件处理必须要在这个限定时间内，通过定时器来为事件处理计时。

   ​		类似于Linux嵌入式系统内存分配C函数 mem_alloc，OSAL 利用 osal_mem_alloc 提供基本的存储管理，但 osal_mem_alloc只有一个用于定义byte数的参数。对应的内存释放函数为 osal_mem_free。

      不同的子系统通过OSAL的消息机制通信。消息即为数据，数据种类和长度都不限定。消息收发过程描述如下：

      接收信息，调用函数osal_msg_allocate创建消息占用内存空间（已经包含了osal_mem_alloc函数功能），需要为该函数指定空间大小，该函数返回内存空间地址指针，利用该指针就可把所需数据拷贝到该空间。

      发送数据，调用函数osal_msg_send，需为该函数指定发送目标任务，OSAL通过旗语SYS_EVENT_MSG告知目标任务，目标任务的处理函数调用osal_msg_receive来接收发来的数据。

      建议每个OSAL任务都有一个消息处理函数，每当任务收到一个消息后，通过消息的种类来确定需要本任务做相应处理。消息接收并处理完成，调用函数osal_msg_deallocate来释放内存（已经包含了osal_mem_free函数功能）。

      为了实现更好的移植性，协议栈将硬件层抽象出了一个HAL硬件抽象层，当新的硬件平台做好后，只需修改HAL，而不需修改HAL之上的协议栈的其他组件和应用程序。

   ![img](https://img-blog.csdnimg.cn/20190602194027154.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDIxNjA2NQ==,size_16,color_FFFFFF,t_70)

   OSAL 中消息队列的构成：

   ![img](https://img-blog.csdnimg.cn/20190602194121954.png)

