





### **volatile**

 

   **Volatile**用来对共享变量的访问进行同步。上一次写入操作的结果对下一次读取操作是肯定可见的。   

​	在写入volatile变量值后，CPU缓存中的内容会被写回主存中；在读取volatile变量时，CPU缓存中的对应内容会被置为失效状态，重新从主存中读取。   

​	将变量声明为volatile相当于单个变量的读取和写入添加了同步操作。但是volatile在使用时不需要利用锁机制，因此性能要优于synchronized关键词。   

​	Volatile的主要作用是确保对一个变量的修改被正确地传播到其他线程中。

​	常使用volatile变量的一个场景是把作为循环结束的判断条件的变量声明为volatile。   

 

### **synchronized**

 

   **Synchronized**是基本的线程同步方式之一。Synchronized可以添加到方法或代码块上。主要用来实现**线程之间的互斥**，即同一时刻只有一个线程允许执行特定的代码，来保证多个线程访问共享变量时的正确性。   

​	声明synchronized的方法或代码块在同一时刻只能有一个线程允许访问的。如果当前已经有线程正在访问synchronized方法或代码块，那么其他试图访问该方法或代码块的线程会处于等待状态。这种互斥性使该方法或代码块中的代码逻辑实际上成为了一个原子操作。   

​	所有的java对象都有一个与之关联的**监视器对象**(**monitor**),允许线程在该监视器对象上进行加锁和解锁操作。每个synchronized关键词在使用时都与一个监视器对象相对应。对于声明为synchronized的方法，**静态方法**对应的监视器对象是所在java类对应的Class类的对象所关联的监视器对象，而**实例方法**使用的是当前对象实例所关联的监视器对象。对于synchronized**代码块**，对应的监视器对象是synchronized代码块声明中的对象所关联的监视器对象。   

​	在一个线程允许执行方法或代码块之前，需先获取对应的监视器对象上的锁。在执行完后，该线程所持有的锁会被自动释放。上一次的解锁操作肯定在下一次的成功加锁操作前发生，由于解锁操作是上一次线程在synchronized方法或代码块中执行的最后一个动作，而加锁是在下一次线程执行synchronized方法或代码块的第一个动作，所以上一次线程运行时对共享变量的修改对下一次线程中的动作是肯定可见的。   

​	当锁被释放时，对共享变量的修改会从CPU缓存中直接写回到主存中；当锁被获取时，CPU缓存中的内容会被置为无效状态，从主存中重新读取共享变量的值。当有线程在执行synchronized方法或代码块时，其他线程由于无法获取锁而处于等待状态，不会影响当前线程的运行。   

```java
public class Jdk7Synchronized {

	private int num = 0;
	private static int value = 0 ;
	
	// 对象的实例方法 同步时使用的监控器对象是当前对象的实例所关联的监控器对象
	public synchronized int addNum(){
		return num++ ;
	}
	
	public int getAddNum(){
		// 代码块 同样使用的是当前对象的实例this所关联的监控器对象
		synchronized (this) {
			return num++;
		}
	}
	
	// 静态方法 监控器对象是 Jdk7Synchronized.class所关联的监控器对象
	public synchronized static int getValuePlus(){
		return value++;
	}
}

```



 

关键字**synchronized**和**volatile**进行比较：

​	A、  关键字volatile是线程同步的轻量级实现，所以性能会比synchronized要好。   

​	B、  Volatile只能修饰变量，而synchronized可以修饰方法及代码块。   

​	C、  多线程访问volatile不会发生阻塞，而synchronized会发生阻塞。   

​	D、  Volatile能保证数据的可见性，但不能保证原子性；

​		而synchronized可以保证原子性，也可以间接保证可见性，因为它会将私有内存和公有内存中的数据做同步。   

​	E、  Volatile解决的是变量在多线程间的可见性；而synchronized解决的是多个线程间访问资源的同步性。   

 

 

 



