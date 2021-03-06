





## **Thread**

 

### **线程状态**

 

   在一个Thread类的对象被创建出来后，它可能处于不同的状态。进行与线程相关的不同操作可能导致Thread类的对象所处的状态有所改变。

不同的线程状态由枚举类型**Thread.State**来表示：   

​	1）  **NEW**:线程刚被创建出来   

​	2）  **RUNNABLE**:线程处于可运行的状态   

​	3）  **BLOCKED**:线程在等待一个监视器对象上的锁时的状态   

​	4）  **WAITING**:调用某些方法会使当前线程进入等待状态   

​	5）  **TIMED_WAITING**:类似WAITING,但是增加了指定的超时时间。时间已过，则退出等待   

​	6）  **TERMINATED**:线程的运行已终止   

 

### **Join**

 

   Thread类的join方法提供了一种简单的同步方法，允许当前线程等待另一个线程运行结束。   

 

### **Sleep**

 

   Thread类的静态方法sleep可以让当前线程进入睡眠状态一段时间，线程的代码执行会暂停，但是线程不会释放所持有的锁。因此不要把对sleep的调用放在synchronized方法或代码块内，否则会造成其他等待获取锁的线程长时间处于等待状态。   

 

### **yield**

 

   若当前线程因某些原因无法继续执行，那么可以使用yield方法来尝试让出所占用的CPU资源，让其他线程获取运行的机会。调用yield方法对操作系统上的调度器来说只是一个信号，但调度器不一定会立即进行线程切换。   

 

 





