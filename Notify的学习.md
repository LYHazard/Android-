# Notify的学习

### notify 通知的两种方式

***

1、GATT_Notification

在从机代码中使用，由从机主动通知，且不需要主机发出请求和回应。

2、GATTServAPP_ProcessCharCfg

在从机代码中使用，需要主机发送一次**通知请求**个从机，从机收到通知请求才发送通知。实际上这个函数里面依然会调用GATT_Notification这个函数。

### 什么是CCC

***

Client Characteristic Configuration，俗称CCC。

notify属性的特征值，会多读、写属性的特征值多一个CCC。

从机要想使用notify函数时能正常发送出数据，就必须保证CCC是被打开的。

### CCC如何打开

***

notify开关可由主机端或者从机端打开，但应尽量保证由主机来打开比较合适，毕竟它是“主机”，“主机“就该有主动权。

1）主机端打开（推荐）

先获取到CCC的特征值句柄，然后利用CCC的特征值句柄往CCC的特征值中写入0x0001。

2）从机端打开（不推荐）

```c
GATTServApp_WriteCharCfg(connHandle, simpleProfileChar4Config, 0x0001);
```


注，如果上面的0x0001改为0x0000，则为关闭notify开关。
	

4、如何获取CCC的句柄？

答：先获取到这个CCC所属的特征值的特征值句柄，然后将该特征值句柄+1。

例如，想要获取到char6的CCC的句柄，我就必须先获取到char6的特征值句柄（参考本博客博文《CC2541之发现服务与特征值》），比如获取到的值是0x0035，则CCC的特征值句柄就是0x0036。之所以加1，是因为char6的CCC所在属性表的位置，正好在char6的特征值后面。
