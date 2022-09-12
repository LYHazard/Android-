# 图解Android事件分发机制

https://www.jianshu.com/p/e99b5e8bd67b?u_atoken=997ee937-43ea-4b25-ab3f-fdd2bcfff58d&u_asession=01K3extfv_W3AsMsgEQYHkHrnBVTcc7yJ8AGHCQkOaaGJvSVTZDUIf-juYcBbEfyysX0KNBwm7Lovlpxjd_P_q4JsKWYrT3W_NKPr8w6oU7K8eACDolYVNF5DvcPumQKMTzdjoMV1y19BFQvaXcOyBfmBkFo3NEHBv0PZUm6pbxQU&u_asig=05cR52hcjdMgru4HQOxSwv19bSqxIag3Icaw0nXlvdvnQDfelrBtvBrTQCw6HkbIfGnJ0IfRW6LIqpX-NdLCcJBQOr2VFPYZZh07IUb45NJMYK_edpdwI8IwGU4XBdWhg1JH4qh7kZl2Cv1Qr4uxw5vycbqkgB6-34dVVCDUX79j39JS7q8ZD7Xtz2Ly-b0kmuyAKRFSVJkkdwVUnyHAIJzTyiQOeLVjNkxe0DqCLdGWEPL0sLWgm0aN_xJXws6gHpqBR97QLsOYcZJeUxi-_JXu3h9VXwMyh6PgyDIVSG1W9cmKpAW_kvjAZBJ2JFfss5D_gPbOU4UBeR99aaYDrx2vVXhWODZ6055tC_Wt6ajym2x3rpgc0rS_FsMnV13quSmWspDxyAEEo4kbsryBKb9Q&u_aref=fVj6mXG4xQDgqMZ2X0FXU9CA7dE%3D

## 一、ACTION_DOWN的情况

![image-20220908142141627](C:\Users\22859\AppData\Roaming\Typora\typora-user-images\image-20220908142141627.png)

* **图分为三层，从上往下依次时Activity、ViewGroup、View**

如图所示由事件流走向可得出几个结论

1、**如果事件不被中断，整个事件流向是一个类U型图**

![image-20220908142858205](C:\Users\22859\AppData\Roaming\Typora\typora-user-images\image-20220908142858205.png)

如果我们没有对控件里面的方法进行重写或更改返回值，而直接用super调用父类的默认实现，那么事件流向应该是从Activity-->ViewGroup-->View从上往下调用dispatchTouchEvent,一直到叶子节点（View）的时候，再由View-->ViewGroup-->Activity从下往上调用onTouchEvent方法。

2、**dispatchTouchEvent和onTouchEvent一旦return true，事件就停止传递了（到达终点）（没有谁能再收到这个事件）**。看图1就能发现只要return true事件就没再继续传下去，**对于return true我们常说事件被消费了，消费了的意思就是事件走到这里就是终点，不会往下传，没有谁能再收到这个事件了**。

3、

* **dispatchTouchEvent和onTouchEvent return false的时候事件都回传给父控件的onTouchEvent处理***
* **对于dispatchTouchEvent返回false的含义应该是：事件停止往子View传递和分发同时开始往父控件回溯（父控件的onTouchEvent开始从下往上回传直到某个onTouchEvent return true）,事件分发机制就像递归，return false 的意义就是递归停止后开始回溯**
* **对于onTouchEvent return false**就比较简单了，它就是不消费事件，并让事件继续往父控件的方向从下往上流。

4、**dispatchTouchEvent、onTouchEvent、onInterceptTouchEvent （ViewGroup和View的这些方法的默认实现就是会让整个事件按照U型完整走完，所以return super.xxx()就会让事件依照U型的方向完整走完整个事件流动路径），中间不做任何改动，不回溯、不终止，每个环节都走到**

5、onInerceptTouchEvent的作用

Intercept的意思就是拦截，每个ViewGroup每次在做分发的时候，问一问拦截器要不要拦截（也就是问问自己这个事件要不要自己来处理）如果要自己处理那就再onInerceptTouchEvent方法中return true就会交给自己的onTouchEvent的处理，如果不拦截就是继续往子控件往下传。**默认是不会去拦截的，因为子View也需要这个事件，所以onInterceptTouchEvent拦截器return super.onInterceptTouchEvent()和return false是一样的，是不会拦截的，事件会继续往子View的dispatchTouchEvent传递**

6、ViewGroup和View的dispatchTouchEvent方法返回super.dispatchTouchEvent()的时候事件流的走向

首先是ViewGroup的dispatchTouchEvent，之前说的return true是终结传递。return false是回溯到父View的onTouchEvent，**然后ViewGroup怎样通过dispatchTouchEvent方法能把事件分发到自己的onTouchEvent处理呢，return true和false都不行，那么只能通过Interceptor把事件拦截下来给自己的onTouchEvent,所以ViewGroup    dispatchTouchEvent方法的super默认实现就是去调用onInterceptTouchEvent，记住这一点！**

那么对于 **View**的dispatchTouchEvent return super.dispatchTouchEvent()的时候呢事件会传到哪里呢，因为View没有拦截器。**但是同样的道理return true是终结。return false是回溯到父类的onTouchEvent，怎样把事件分发到自己的onTouchEvent处理呢，那只能return super.dispatchTouchEvent，View类的dispatchTouchEvent()方法默认实现就是能帮你调用View自己的onTouchEvent方法的**



最后总结

* 对于 dispatchTouchEvent，onTouchEvent，return true是终结事件传递。return false 是回溯到父View的onTouchEvent方法。
* ViewGroup 想把自己分发给自己的onTouchEvent，需要拦截器onInterceptTouchEvent方法return true 把事件拦截下来。
* ViewGroup 的拦截器onInterceptTouchEvent 默认是不拦截的，所以return super.onInterceptTouchEvent()=return false；
* View 没有拦截器，为了让View可以把事件分发给自己的onTouchEvent，View的dispatchTouchEvent默认实现（super）就是把事件分发给自己的onTouchEvent。

## 二、关于ACTION_MOVE和ACTION_UP

​	ACTION_MOVE和ACTION_UP在传递的过程中并不是和ACTION_DOWN 一样，你在执行ACTION_DOWN的时候返回了false，后面一系列其它的action就不会再得到执行了。简单的说，就是当dispatchTouchEvent在进行事件分发的时候，只有前一个事件（如ACTION_DOWN）返回true，才会收到ACTION_MOVE和ACTION_UP的事件。

​	上面提到过，事件如果不被打断的话是会不断往下传到叶子层（View）,然后不断回传到Activity，dispatchTouchEvent和onTouhEvent可以通过return true消费事件，终结事件传递，而onInterceptTouchEvent并不能消费事件，它相当于是一个分叉口起到了分流导流的作用，ACTION_MOVE和ACTION_UP会在哪些函数呗调用，之前说了并不是哪个函数收到了ACTION_DOWN，就会收到ACTION_DOWN后续的事件的。

下面通过几张图看看不同场景下，ACTION_MOVE事件和ACTION_UP事件的具体走向并总结一下规律



### **2.1、我们在ViewGroup1的dispatchTouchEvent方法返回true消费这次事件**

ACTION_DOWN事件从（Activity的dispatchTouchEvent）----->(ViewGroup1的dispatchTouchEvent)后结束传递，事件被消费（如下图红色的箭头代表ACTION_DOWN事件的流向）

```rust
//打印日志
Activity | dispatchTouchEvent --> ACTION_DOWN 
ViewGroup1 | dispatchTouchEvent --> ACTION_DOWN
---->消费
```

![image-20220908161147681](C:\Users\22859\AppData\Roaming\Typora\typora-user-images\image-20220908161147681.png)

在这种场景下ACTION_MOVE和ACTION_UP将如何呢，看下面的打出来的日志

```rust
Activity | dispatchTouchEvent --> ACTION_MOVE 
ViewGroup1 | dispatchTouchEvent --> ACTION_MOVE
----
TouchEventActivity | dispatchTouchEvent --> ACTION_UP 
ViewGroup1 | dispatchTouchEvent --> ACTION_UP
----
```

下图中
红色的箭头代表ACTION_DOWN 事件的流向
蓝色的箭头代表ACTION_MOVE 和 ACTION_UP 事件的流向

![image-20220908161451561](C:\Users\22859\AppData\Roaming\Typora\typora-user-images\image-20220908161451561.png)

### **2.2 在ViewGroup2的dispatchTouchEvent返回true消费这次事件**

```rust
Activity | dispatchTouchEvent --> ACTION_DOWN 
ViewGroup1 | dispatchTouchEvent --> ACTION_DOWN
ViewGroup1 | onInterceptTouchEvent --> ACTION_DOWN
ViewGroup2 | dispatchTouchEvent --> ACTION_DOWN
---->消费
Activity | dispatchTouchEvent --> ACTION_MOVE 
ViewGroup1 | dispatchTouchEvent --> ACTION_MOVE
ViewGroup1 | onInterceptTouchEvent --> ACTION_MOVE
ViewGroup2 | dispatchTouchEvent --> ACTION_MOVE
----
TouchEventActivity | dispatchTouchEvent --> ACTION_UP 
ViewGroup1 | dispatchTouchEvent --> ACTION_UP
ViewGroup1 | onInterceptTouchEvent --> ACTION_UP
ViewGroup2 | dispatchTouchEvent --> ACTION_UP
----
```

红色的箭头代表ACTION_DOWN 事件的流向
蓝色的箭头代表ACTION_MOVE 和 ACTION_UP 事件的流向

![image-20220908162456191](C:\Users\22859\AppData\Roaming\Typora\typora-user-images\image-20220908162456191.png)

### 2.3 **我们在View 的dispatchTouchEvent 返回true消费这次事件**

 这个我不就画图了，效果和在ViewGroup2 的dispatchTouchEvent return true的差不多，同样的收到ACTION_DOWN 的dispatchTouchEvent函数都能收到 ACTION_MOVE和ACTION_UP。
 **所以我们就基本可以得出结论如果在某个控件的dispatchTouchEvent 返回true消费终结事件，那么收到ACTION_DOWN 的函数也能收到 ACTION_MOVE和ACTION_UP。**

### 2.4 我们在View的onTouchEvent返回true消费这次事件

红色的箭头代表ACTION_DOWN 事件的流向
蓝色的箭头代表ACTION_MOVE 和 ACTION_UP 事件的流向

![image-20220908162754557](C:\Users\22859\AppData\Roaming\Typora\typora-user-images\image-20220908162754557.png)

### 2.5 **我们在ViewGroup 2 的onTouchEvent 返回true消费这次事件**

红色的箭头代表ACTION_DOWN 事件的流向
蓝色的箭头代表ACTION_MOVE 和 ACTION_UP 事件的流向

![image-20220908162843982](C:\Users\22859\AppData\Roaming\Typora\typora-user-images\image-20220908162843982.png)

### 2.6**我们在ViewGroup 1 的onTouchEvent 返回true消费这次事件**

红色的箭头代表ACTION_DOWN 事件的流向
蓝色的箭头代表ACTION_MOVE 和 ACTION_UP 事件的流向

![image-20220908163107671](C:\Users\22859\AppData\Roaming\Typora\typora-user-images\image-20220908163107671.png)

### 2.7  **我们在Activity 的onTouchEvent 返回true消费这次事件**

红色的箭头代表ACTION_DOWN 事件的流向
蓝色的箭头代表ACTION_MOVE 和 ACTION_UP 事件的流向

![image-20220908163201033](C:\Users\22859\AppData\Roaming\Typora\typora-user-images\image-20220908163201033.png)

### 2.8  我们在View的dispatchTouchEvent 返回false并且Activity 的onTouchEvent 返回true消费这次事件

红色的箭头代表ACTION_DOWN 事件的流向
蓝色的箭头代表ACTION_MOVE 和 ACTION_UP 事件的流向

![image-20220908163850630](C:\Users\22859\AppData\Roaming\Typora\typora-user-images\image-20220908163850630.png)



### 2.9  我们在View的dispatchTouchEvent 返回false并且ViewGroup 1 的onTouchEvent 返回true消费这次事件 

![image-20220908163940372](C:\Users\22859\AppData\Roaming\Typora\typora-user-images\image-20220908163940372.png)

### 2.10 我们在View的dispatchTouchEvent 返回false并且在ViewGroup 2 的onTouchEvent 返回true消费这次事件

![image-20220908164023821](C:\Users\22859\AppData\Roaming\Typora\typora-user-images\image-20220908164023821.png)

### 2.11  我们在ViewGroup2的dispatchTouchEvent 返回false并且在ViewGroup1 的onTouchEvent返回true消费这次事件

![image-20220908164049570](C:\Users\22859\AppData\Roaming\Typora\typora-user-images\image-20220908164049570.png)

###  2.12 我们在ViewGroup2的onInterceptTouchEvent 返回true拦截此次事件并且在ViewGroup 1 的onTouchEvent返回true消费这次事件。

![image-20220908164401963](C:\Users\22859\AppData\Roaming\Typora\typora-user-images\image-20220908164401963.png)

对于在onTouchEvent消费事件的情况：**在哪个View的onTouchEvent 返回true，那么ACTION_MOVE和ACTION_UP的事件从上往下传到这个View后就不再往下传递了，而直接传给自己的onTouchEvent 并结束本次事件传递过程。**

对于ACTION_MOVE、ACTION_UP总结：**ACTION_DOWN事件在哪个控件消费了（return true），  那么ACTION_MOVE和ACTION_UP就会从上往下（通过dispatchTouchEvent）做事件分发往下传，就只会传到这个控件，不会继续往下传，如果ACTION_DOWN事件是在dispatchTouchEvent消费，那么事件到此为止停止传递，如果ACTION_DOWN事件是在onTouchEvent消费的，那么会把ACTION_MOVE或ACTION_UP事件传给该控件的onTouchEvent处理并结束传递。**