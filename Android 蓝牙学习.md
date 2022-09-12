# Android 蓝牙学习

## 一、蓝牙概述

  Android 平台包含蓝牙网络堆栈支持，此支持能让设备以无线方式与其他蓝牙设备交换数据。应用框架提供通过 Android Bluetooth API 访问蓝牙功能的权限。这些 API 允许应用以无线方式连接到其他蓝牙设备，从而实现点到点和多点无线功能。

​	Android应用可通过Bluetooth API执行以下操作：

* 扫描其他蓝牙设备
* 查询本地蓝牙适配器的配对蓝牙设备
* 建立RFCOMM通道
* 通过服务发现连接到其他设备
* 与其他设备进行双向数据传输
* 管理多个连接

## 二、基础知识

​	为了让支持蓝牙的设备能够在彼此之间传输数据，它们必须先通过*配对*过程形成通信通道。其中一台设备（*可检测到的设备*）需将自身设置为可接收传入的连接请求。另一台设备会使用*服务发现*过程找到此可检测到的设备。在可检测到的设备接受配对请求后，这两台设备会完成*绑定*过程，并在此期间交换安全密钥。二者会缓存这些密钥，以供日后使用。完成配对和绑定过程后，两台设备会交换信息。当会话完成时，发起配对请求的设备会发布已将其链接到可检测设备的通道。但是，这两台设备仍保持绑定状态，因此在未来的会话期间，只要二者在彼此的范围内且均未移除绑定，便可自动重新连接。

### 2.1 蓝牙权限

如果要在应用中使用蓝牙功能，必须声明两个权限，第一个是**BLUETOOTH**。需要此权限才能执行任何蓝牙通信，例如请求连接、接受连接和传输数据等。

第二个必须声明的权限是**`ACCESS_FINE_LOCATION`**。应用需要此权限，因为蓝牙扫描可用于收集用户的位置信息。此类信息可能来自用户自己的设备，以及在商店和交通设施等位置使用的蓝牙信标。

如果想让应用启动设备发现或操作蓝牙设置，则除了`BLUETOOTH`权限之外，还必须声明**`BLUETOOTH_ADMIN`**权限。大多数应用只是需利用此权限发现本地蓝牙设备，除非应用是根据用户请求修改蓝牙设备的“超级管理员”，否则不应使用此权限所授予的其他功能。

### 2.2 使用配置文件

从 Android 3.0 开始，**Bluetooth API** 便支持使用蓝牙配置文件。*蓝牙配置文件*是适用于设备间蓝牙通信的无线接口规范。举个例子：免提配置文件。如果手机要与无线耳机进行连接，则两台设备都必须支持免提配置文件。

Android Bluetooth API 为以下蓝牙配置文件提供实现：

* **耳机**：耳机配置文件可为蓝牙耳机提供支持，以便与手机配合使用。Android提供**`BluetoothHeadset`**类，该类是用于控制蓝牙耳机服务的代理。其中包括蓝牙耳机和免提 (v1.5) 的配置文件。**`BluetoothHeadset`** 类包含对 AT 命令的支持。
* **A2DP：**蓝牙立体声音频传输配置文件 (A2DP) 定义如何通过蓝牙连接和流式传输，将高质量音频从一个设备传输至另一个设备。Android 提供 **`BluetoothA2dp`** 类，该类是用于控制蓝牙 A2DP 服务的代理。
* **健康设备**。Android 4.0（API 级别 14）引入了对蓝牙健康设备配置文件 (HDP) 的支持。该配置文件允许您创建应用，从而使用蓝牙与支持蓝牙功能的健康设备（例如心率监测仪、血糖仪、温度计、台秤等）进行通信。

以下是配置文件的基本步骤：

1.获取默认适配器

2.设置 `BluetoothProfile.ServiceListener`。此侦听器会在 `BluetoothProfile` 客户端连接到服务或断开服务连接时向其发送通知。

3.使用 `getProfileProxy()` 与配置文件所关联的配置文件代理对象建立连接。在以下示例中，配置文件代理对象是一个 `BluetoothHeadset` 实例。

4.在 `onServiceConnected()` 中，获取配置文件代理对象的句柄。

5.获得配置文件代理对象后，您可以用其监视连接状态，并执行与该配置文件相关的其他操作。

## 三、设置蓝牙

如果设备不支持蓝牙，则应正常停用任何蓝牙功能。如果设备支持蓝牙但是已停用此功能，则可以请求用户在不离开应用的同时启动蓝牙。借助`BluetoothAdapter`，可以分两步完成设置：

1. 获取`BluetoothAdapter`

​		所有蓝牙Activity都需要`BluetoothAdapter`。如要获取`BluetoothAdapter`,请调用静态的请调用静态的 `getDefaultAdapter()` 方法。此方法会返回一个 `BluetoothAdapter` 对象，表示设备自身的蓝牙适配器（蓝牙无线装置）。整个系统只有一个蓝牙适配器，并且您的应用可使用此对象与之进行交互。如果 `getDefaultAdapter()` 返回 `null`，则表示设备不支持蓝牙。

```java
BluetoothAdapter bluetoothAdapter = BluetoothAdapter.getDefaultAdapter();
if (bluetoothAdapter == null) {
    // Device doesn't support Bluetooth
}
```

2.启用蓝牙

 下一步，您需要确保已启用蓝牙。调用 `isEnabled()`，以检查当前是否已启用蓝牙。如果此方法返回 false，则表示蓝牙处于停用状态。如要请求启用蓝牙，请调用 `startActivityForResult()`，从而传入一个 `ACTION_REQUEST_ENABLE` Intent 操作。此调用会发出通过系统设置启用蓝牙的请求（无需停止应用）。例如：

```java
if (!bluetoothAdapter.isEnabled()) {
    Intent enableBtIntent = new Intent(BluetoothAdapter.ACTION_REQUEST_ENABLE);
    startActivityForResult(enableBtIntent, REQUEST_ENABLE_BT);
}
```

1. 传递给 `startActivityForResult()` 的 `REQUEST_ENABLE_BT` 常量为局部定义的整型数（必须大于 0）。系统会以 `onActivityResult()` 实现中的 `requestCode` 参数形式，向您传回该常量。

   如果成功启用蓝牙，您的 Activity 会在 `onActivityResult()` 回调中收到 `RESULT_OK` 结果代码。如果由于某个错误（或用户响应“No”）未成功启用蓝牙，则结果代码为 `RESULT_CANCELED`。

您的应用还可选择侦听 `ACTION_STATE_CHANGED` 广播 Intent，每当蓝牙状态发生变化时，系统都会广播此 Intent。此广播包含额外字段 、`EXTRA_STATE` 和 `EXTRA_PREVIOUS_STATE`，二者分别包含新的和旧的蓝牙状态。这些额外字段可能为以下值：`STATE_TURNING_ON`、`STATE_ON`、`STATE_TURNING_OFF` 和 `STATE_OFF`。如果应用需检测对蓝牙状态所做的运行时更改，侦听此广播。

## 四、查找设备

利用 `BluetoothAdapter`，您可以通过设备发现或查询配对设备的列表来查找远程蓝牙设备。

设备发现是一个扫描过程，它会搜索局部区域内已启用蓝牙功能的设备，并请求与每台设备相关的某些信息。此过程有时也被称为*发现*、*查询*或*扫描*。但是，只有在当下接受信息请求时，附近区域的蓝牙设备才会通过启用*可检测性*响应发现请求。如果设备已启用可检测性，它会通过共享一些信息（例如设备的名称、类及其唯一的 MAC 地址）来响应发现请求。借助此类信息，执行发现过程的设备可选择发起对已检测到设备的连接。

在首次与远程设备建立连接后，系统会自动向用户显示配对请求。当设备完成配对后，系统会保存关于该设备的基本信息（例如设备的名称、类和 MAC 地址），并且可使用 Bluetooth API 读取这些信息。借助远程设备的已知 MAC 地址，您可以随时向其发起连接，而无需执行发现操作（假定该设备仍处于有效范围内）。

请注意，被配对与被连接之间存在区别：

* 被配对是指两台设备知晓彼此的存在，具有可用于身份验证的共享链路秘钥，并且能够与彼此建立加密连接。
* 被连接是指设备当前共享一个RFCOMM通道，并且能够向彼此传输数据。当前的Android Bluetooth API要求规定，只有先对设备进行配对，然后才能建立RFCOMM连接。在使用Bluetooth API发起加密连接时，系统会自动执行配对。

### 4.1查询已配对设备

在执行设备发现之前，您必须查询已配对的设备集，以了解所需的设备是否处于已检测到状态。为此，请调用 `getBondedDevices()`。此方法会返回一组表示已配对设备的 `BluetoothDevice` 对象。例如，您可以查询所有已配对设备，并获取每台设备的名称和 MAC 地址，如以下代码段所示：

```Java
Set<BluetoothDevice> pairedDevices = bluetoothAdapter.getBondedDevices();

if (pairedDevices.size() > 0) {
    // There are paired devices. Get the name and address of each paired device.
    for (BluetoothDevice device : pairedDevices) {
        String deviceName = device.getName();
        String deviceHardwareAddress = device.getAddress(); // MAC address
    }
}
```

如要发起与蓝牙设备的连接，只需从关联的 `BluetoothDevice` 对象获取 MAC 地址，可通过调用 `getAddress()` 检索此地址。有关创建连接的详情，请参阅连接设备部分。

### 4.2 发现设备

如要开始发现设备，只需调用`startDiscovery()`。该进程为异步操作，并且会返回一个布尔值，指示发现进程是否已成功启动。发现进程通常包含约 12 秒钟的查询扫描，随后会对发现的每台设备进行页面扫描，以检索其蓝牙名称。

您的应用必须针对 `ACTION_FOUND` Intent 注册一个 BroadcastReceiver，以便接收每台发现的设备的相关信息。系统会为每台设备广播此 Intent。Intent 包含额外字段 `EXTRA_DEVICE` 和 `EXTRA_CLASS`，二者又分别包含 `BluetoothDevice` 和 `BluetoothClass`。以下代码段展示如何在发现设备时通过注册来处理广播：

```Java
@Override
protected void onCreate(Bundle savedInstanceState) {
    ...

    // Register for broadcasts when a device is discovered.
    IntentFilter filter = new IntentFilter(BluetoothDevice.ACTION_FOUND);
    registerReceiver(receiver, filter);
}

// Create a BroadcastReceiver for ACTION_FOUND.
private final BroadcastReceiver receiver = new BroadcastReceiver() {
    public void onReceive(Context context, Intent intent) {
        String action = intent.getAction();
        if (BluetoothDevice.ACTION_FOUND.equals(action)) {
            // Discovery has found a device. Get the BluetoothDevice
            // object and its info from the Intent.
            BluetoothDevice device = intent.getParcelableExtra(BluetoothDevice.EXTRA_DEVICE);
            String deviceName = device.getName();
            String deviceHardwareAddress = device.getAddress(); // MAC address
        }
    }
};

@Override
protected void onDestroy() {
    super.onDestroy();
    ...

    // Don't forget to unregister the ACTION_FOUND receiver.
    unregisterReceiver(receiver);
}
```

如要发起与蓝牙设备的连接，只需从关联的 `BluetoothDevice` 对象获取 MAC 地址，可通过调用 `getAddress()` 检索此地址。有关创建连接的详情，请参阅连接设备部分。

### 4.3启用可检测性

如果您希望将本地设备设为可被其他设备检测到，请使用 `ACTION_REQUEST_DISCOVERABLE` Intent 调用 `startActivityForResult(Intent, int)`。这样便可发出启用系统可检测到模式的请求，从而无需导航至设置应用，避免暂停使用您的应用。默认情况下，设备处于可检测到模式的时间为 120 秒（2 分钟）。通过添加 `EXTRA_DISCOVERABLE_DURATION` Extra 属性，您可以定义不同的持续时间，最高可达 3600 秒（1 小时）。

以下代码段将设备处于可检测到模式的时间设置为 5 分钟（300 秒）：

```Java
Intent discoverableIntent =
        new Intent(BluetoothAdapter.ACTION_REQUEST_DISCOVERABLE);
discoverableIntent.putExtra(BluetoothAdapter.EXTRA_DISCOVERABLE_DURATION, 300);
startActivity(discoverableIntent);
```

设备将在分配的时间内以静默方式保持可检测到模式。如果您希望在可检测到模式发生变化时收到通知，则可以为 `ACTION_SCAN_MODE_CHANGED` Intent 注册 BroadcastReceiver。此 Intent 将包含额外字段 `EXTRA_SCAN_MODE` 和 `EXTRA_PREVIOUS_SCAN_MODE`，二者分别提供新的和旧的扫描模式。每个 Extra 属性可能拥有以下值：

- `SCAN_MODE_CONNECTABLE_DISCOVERABLE`

  设备处于可检测到模式。

- `SCAN_MODE_CONNECTABLE`

  设备未处于可检测到模式，但仍能收到连接。

- `SCAN_MODE_NONE`

  设备未处于可检测到模式，且无法收到连接。

如果您要发起对远程设备的连接，则无需启用设备可检测性。只有当您希望应用对接受传入连接的服务器套接字进行托管时，才有必要启用可检测性，因为在发起对其他设备的连接之前，远程设备必须能够发现这些设备。

## 五、连接设备

如要在两台设备之间创建连接，您必须同时实现服务器端和客户端机制，因为其中一台设备必须开放服务器套接字，而另一台设备必须使用服务器设备的 MAC 地址发起连接。服务器设备和客户端设备均会以不同方法获得所需的 `BluetoothSocket`。接受传入连接后，服务器会收到套接字信息。在打开与服务器相连的 RFCOMM 通道时，客户端会提供套接字信息。

当服务器和客户端在同一 RFCOMM 通道上分别拥有已连接的 `BluetoothSocket` 时，即可将二者视为彼此连接。这种情况下，每台设备都能获得输入和输出流式传输，并开始传输数据

### 5.1连接技术

一种实现技术是自动将每台设备准备为一个服务器，从而使每台设备开放一个服务器套接字并侦听连接。在此情况下，任一设备都可发起与另一台设备的连接，并成为客户端。或者，其中一台设备可显式托管连接并按需开放一个服务器套接字，而另一台设备则发起连接。

#### 5.1.1 作为服务器连接

  当需要连接两台设备时，其中一台设备必须保持开放的`BluetoothServerSocket`,从而充当服务器。服务器套接字的用途是侦听传入的连接请求，并在接受请求后提供已连接的`BluetoothSocket`。从 `BluetoothServerSocket` 获取 `BluetoothSocket` 后，您可以（并且应该）舍弃 `BluetoothServerSocket`，除非您的设备需要接受更多连接。

如要设置服务器套接字并接受连接，请依次完成以下步骤：

1. 通过调用`listenUsingRfcommWithServiceRecord()`获取`BluetoothServerSocket`。

   该字符串是服务的可识别名称，系统会自动将其写入到设备上的新服务发现协议 (SDP) 数据库条目。此名称没有限制，可直接使用您的应用名称。SDP 条目中也包含通用唯一标识符 (UUID)，这也是客户端设备连接协议的基础。换言之，当客户端尝试连接此设备时，它会携带 UUID，从而对其想要连接的服务进行唯一标识。为了让服务器接受连接，这些 UUID 必须互相匹配。

   UUID 是一种标准化的 128 位格式，可供字符串 ID 用来对信息进行唯一标识。UUID 的特点是其足够庞大，因此您可以选择任意随机 ID，而不会与其他任何 ID 发生冲突。在本例中，其用于对应用的蓝牙服务进行唯一标识。如要获取供应用使用的 UUID，您可以从网络上的众多随机 UUID 生成器中任选一种，然后使用 `fromString(String)` 初始化一个 `UUID`。

2. 通过调用`accept()`开始侦听连接请求。

   这是一个阻塞调用。当服务器接受连接或异常发生时，该调用便会返回。只有当远程设备发送包含 UUID 的连接请求，并且该 UUID 与使用此侦听服务器套接字注册的 UUID 相匹配时，服务器才会接受连接。连接成功后，`accept()` 将返回已连接的 `BluetoothSocket`。

1. 如果您无需接受更多连接，请调用`close()`。

   此方法调用会释放服务器套接字及其所有资源，但不会关闭 `accept()` 所返回的已连接的 `BluetoothSocket`。与 TCP/IP 不同，RFCOMM 一次只允许每个通道有一个已连接的客户端，因此大多数情况下，在接受已连接的套接字后，您可以立即在 `BluetoothServerSocket` 上调用 `close()`。

由于 `accept()` 是阻塞调用，因此您不应在主 Activity 界面线程中执行该调用，这样您的应用才仍然可以响应其他用户的交互。通常，您可以在应用所管理的新线程中完成所有涉及 `BluetoothServerSocket` 或 `BluetoothSocket` 的工作。如要取消 `accept()` 等被阻塞的调用，请通过另一个线程，在 `BluetoothServerSocket` 或 `BluetoothSocket` 上调用 `close()`。请注意，`BluetoothServerSocket` 或 `BluetoothSocket` 中的所有方法都是线程安全的方法。

服务器组件可通过以下简化线程接受传入连接：

```Java
private class AcceptThread extends Thread {
    private final BluetoothServerSocket mmServerSocket;

    public AcceptThread() {
        // Use a temporary object that is later assigned to mmServerSocket
        // because mmServerSocket is final.
        BluetoothServerSocket tmp = null;
        try {
            // MY_UUID is the app's UUID string, also used by the client code.
            tmp = bluetoothAdapter.listenUsingRfcommWithServiceRecord(NAME, MY_UUID);
        } catch (IOException e) {
            Log.e(TAG, "Socket's listen() method failed", e);
        }
        mmServerSocket = tmp;
    }

    public void run() {
        BluetoothSocket socket = null;
        // Keep listening until exception occurs or a socket is returned.
        while (true) {
            try {
                socket = mmServerSocket.accept();
            } catch (IOException e) {
                Log.e(TAG, "Socket's accept() method failed", e);
                break;
            }

            if (socket != null) {
                // A connection was accepted. Perform work associated with
                // the connection in a separate thread.
                manageMyConnectedSocket(socket);
                mmServerSocket.close();
                break;
            }
        }
    }

    // Closes the connect socket and causes the thread to finish.
    public void cancel() {
        try {
            mmServerSocket.close();
        } catch (IOException e) {
            Log.e(TAG, "Could not close the connect socket", e);
        }
    }
}
```

在此示例中，只需要一个传入连接，因此在接受连接并获取 `BluetoothSocket` 之后，应用会立即将获取的 `BluetoothSocket` 传送到单独的线程、关闭 `BluetoothServerSocket` 并中断循环。

请注意，如果 `accept()` 返回 `BluetoothSocket`，则表示已连接套接字。因此，您不应像从客户端那样调用 `connect()`。

应用特定的 `manageMyConnectedSocket()` 方法旨在启动用于传输数据的线程（详情请参阅[管理连接](https://developer.android.google.cn/guide/topics/connectivity/bluetooth#ManageAConnection)部分）。

通常，在完成传入连接的侦听后，您应立即关闭您的 `BluetoothServerSocket`。在此示例中，获取 `BluetoothSocket` 后会立即调用 `close()`。此外，您可能还希望在线程中提供一个公共方法，以便在需要停止侦听服务器套接字时关闭私有 `BluetoothSocket`。

####  5.1.2 作为客户端连接

如果远程设备在开放服务器套接字上接受连接，则为了发起与此设备的连接，您必须首先获取表示该远程设备的 `BluetoothDevice` 对象。如要了解如何创建 `BluetoothDevice`，请参阅[查找设备](https://developer.android.google.cn/guide/topics/connectivity/bluetooth#FindingDevices)。然后，您必须使用 `BluetoothDevice` 来获取 `BluetoothSocket` 并发起连接。

基本步骤如下所示：

1. 使用`BluetoothDevice`，通过调用 `createRfcommSocketToServiceRecord(UUID)`获取`BluetoothSocket`。

   此方法会初始化 `BluetoothSocket` 对象，以便客户端连接至 `BluetoothDevice`。此处传递的 UUID 必须与服务器设备在调用 `listenUsingRfcommWithServiceRecord(String, UUID)` 开放其 `BluetoothServerSocket` 时所用的 UUID 相匹配。如要使用匹配的 UUID，请通过硬编码方式将 UUID 字符串写入您的应用，然后通过服务器和客户端代码引用该字符串。

2. 通过调用`connect()`发起连接，请注意，此方法为阻塞调

   当客户端调用此方法后，系统会执行 SDP 查找，以找到带有所匹配 UUID 的远程设备。如果查找成功并且远程设备接受连接，则其会共享 RFCOMM 通道以便在连接期间使用，并且 `connect()` 方法将会返回。如果连接失败，或者 `connect()` 方法超时（约 12 秒后），则此方法将引发 `IOException`。由于 `connect()` 是阻塞调用，因此您应始终在主 Activity（界面）线程以外的线程中执行此连接步骤。

**注意：**您应始终调用 `cancelDiscovery()`，以确保设备在您调用 `connect()` 之前不会执行设备发现。如果正在执行发现操作，则会大幅降低连接尝试的速度，并增加连接失败的可能性。

```java
private class ConnectThread extends Thread {
    private final BluetoothSocket mmSocket;
    private final BluetoothDevice mmDevice;

    public ConnectThread(BluetoothDevice device) {
        // Use a temporary object that is later assigned to mmSocket
        // because mmSocket is final.
        BluetoothSocket tmp = null;
        mmDevice = device;

        try {
            // Get a BluetoothSocket to connect with the given BluetoothDevice.
            // MY_UUID is the app's UUID string, also used in the server code.
            tmp = device.createRfcommSocketToServiceRecord(MY_UUID);
        } catch (IOException e) {
            Log.e(TAG, "Socket's create() method failed", e);
        }
        mmSocket = tmp;
    }

    public void run() {
        // Cancel discovery because it otherwise slows down the connection.
        bluetoothAdapter.cancelDiscovery();

        try {
            // Connect to the remote device through the socket. This call blocks
            // until it succeeds or throws an exception.
            mmSocket.connect();
        } catch (IOException connectException) {
            // Unable to connect; close the socket and return.
            try {
                mmSocket.close();
            } catch (IOException closeException) {
                Log.e(TAG, "Could not close the client socket", closeException);
            }
            return;
        }

        // The connection attempt succeeded. Perform work associated with
        // the connection in a separate thread.
        manageMyConnectedSocket(mmSocket);
    }

    // Closes the client socket and causes the thread to finish.
    public void cancel() {
        try {
            mmSocket.close();
        } catch (IOException e) {
            Log.e(TAG, "Could not close the client socket", e);
        }
    }
}
```

请注意，此段代码在尝试连接之前先调用了 `cancelDiscovery()`。您应始终在 `connect()` 之前调用 `cancelDiscovery()`，这是因为无论当前是否正在执行设备发现，`cancelDiscovery()` 都会成功。但是，如果应用需要确定是否正在执行设备发现，您可以使用 `isDiscovering()` 进行检测。

应用特定 `manageMyConnectedSocket()` 方法旨在启动用于传输数据的线程（详情请参阅[管理连接](https://developer.android.google.cn/guide/topics/connectivity/bluetooth#ManagingAConnection)部分）。

使用完 `BluetoothSocket` 后，请务必调用 `close()`。这样，您便可立即关闭连接的套接字，并释放所有相关的内部资源。

## 六、管理连接

成功连接多台设备后，每台设备都会有已连接的 `BluetoothSocket`。这一点非常有趣，因为这表示您可以在设备之间共享信息。使用 `BluetoothSocket` 传输数据的一般过程如下所示：

1. 使用`getInputStream()` 和 `getOutputStream()`，分别获取通过套接字处理数据传输的 `InputStream` 和 `OutputStream`。
2. 使用 `read(byte[])` 和 `write(byte[])` 读取数据以及将其写入数据流。

当然，您还需考虑实现细节。具体来说，您应使用专门的线程从数据流读取数据，以及将数据写入数据流。这一点非常重要，因为 `read(byte[])` 和 `write(byte[])` 方法都是阻塞调用。`read(byte[])` 方法将会阻塞，直至从数据流中读取数据。`write(byte[])` 方法通常不会阻塞，但若远程设备调用 `read(byte[])` 方法的速度不够快，进而导致中间缓冲区已满，则该方法可能会保持阻塞状态以实现流量控制。因此，线程中的主循环应专门用于从 `InputStream` 中读取数据。您可使用线程中单独的公共方法，发起对 `OutputStream` 的写入操作。

#### 示例

以下示例介绍如何在通过蓝牙连接的两台设备之间传输数据：

```java
public class MyBluetoothService {
    private static final String TAG = "MY_APP_DEBUG_TAG";
    private Handler handler; // handler that gets info from Bluetooth service

    // Defines several constants used when transmitting messages between the
    // service and the UI.
    private interface MessageConstants {
        public static final int MESSAGE_READ = 0;
        public static final int MESSAGE_WRITE = 1;
        public static final int MESSAGE_TOAST = 2;

        // ... (Add other message types here as needed.)
    }

    private class ConnectedThread extends Thread {
        private final BluetoothSocket mmSocket;
        private final InputStream mmInStream;
        private final OutputStream mmOutStream;
        private byte[] mmBuffer; // mmBuffer store for the stream

        public ConnectedThread(BluetoothSocket socket) {
            mmSocket = socket;
            InputStream tmpIn = null;
            OutputStream tmpOut = null;

            // Get the input and output streams; using temp objects because
            // member streams are final.
            try {
                tmpIn = socket.getInputStream();
            } catch (IOException e) {
                Log.e(TAG, "Error occurred when creating input stream", e);
            }
            try {
                tmpOut = socket.getOutputStream();
            } catch (IOException e) {
                Log.e(TAG, "Error occurred when creating output stream", e);
            }

            mmInStream = tmpIn;
            mmOutStream = tmpOut;
        }

        public void run() {
            mmBuffer = new byte[1024];
            int numBytes; // bytes returned from read()

            // Keep listening to the InputStream until an exception occurs.
            while (true) {
                try {
                    // Read from the InputStream.
                    numBytes = mmInStream.read(mmBuffer);
                    // Send the obtained bytes to the UI activity.
                    Message readMsg = handler.obtainMessage(
                            MessageConstants.MESSAGE_READ, numBytes, -1,
                            mmBuffer);
                    readMsg.sendToTarget();
                } catch (IOException e) {
                    Log.d(TAG, "Input stream was disconnected", e);
                    break;
                }
            }
        }

        // Call this from the main activity to send data to the remote device.
        public void write(byte[] bytes) {
            try {
                mmOutStream.write(bytes);

                // Share the sent message with the UI activity.
                Message writtenMsg = handler.obtainMessage(
                        MessageConstants.MESSAGE_WRITE, -1, -1, mmBuffer);
                writtenMsg.sendToTarget();
            } catch (IOException e) {
                Log.e(TAG, "Error occurred when sending data", e);

                // Send a failure message back to the activity.
                Message writeErrorMsg =
                        handler.obtainMessage(MessageConstants.MESSAGE_TOAST);
                Bundle bundle = new Bundle();
                bundle.putString("toast",
                        "Couldn't send data to the other device");
                writeErrorMsg.setData(bundle);
                handler.sendMessage(writeErrorMsg);
            }
        }

        // Call this method from the main activity to shut down the connection.
        public void cancel() {
            try {
                mmSocket.close();
            } catch (IOException e) {
                Log.e(TAG, "Could not close the connect socket", e);
            }
        }
    }
}
```

当构造函数获取必要的数据流后，线程会等待通过 `InputStream` 传入的数据。当 `read(byte[])` 返回数据流中的数据时，将使用来自父类的 `Handler` 成员将数据发送到主 Activity。然后，线程会等待从 `InputStream` 中读取更多字节。

发送传出数据不外乎从主 Activity 调用线程的 `write()` 方法，并传入要发送的字节。此方法会调用 `write(byte[])`，从而将数据发送到远程设备。如果在调用 `write(byte[])` 时引发 `IOException`，则线程会发送一条 Toast 至主 Activity，向用户说明设备无法将给定的字节发送到另一台（连接的）设备。

借助线程的 `cancel()` 方法，您可通过关闭 `BluetoothSocket` 随时终止连接。当您结束蓝牙连接的使用时，应始终调用此方法。