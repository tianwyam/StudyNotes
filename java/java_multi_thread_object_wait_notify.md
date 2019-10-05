





## **Object类的(wait/notify/notifyAll)**

 

   除了synchronized互斥访问外，线程之间也需要通过**协作**的方式来完成任务。   

 

### **wait**

 

   **wait**方法是在Object类中定义。作用是使当前线程进入等待状态。   

​	**调用wait方法的含义：**java中的每个对象除了有与之关联的**监控器对象**外，还有一个与之关联的**包含线程的等待集合**。在调用wait方法时，该方法调用的接收者所关联的监视器对象是所使用的监视器对象，同时wait方法所影响的是执行wait方法调用的当前线程。成功调用wait方法的先决条件是当前线程获取到监视器对象的锁。若没有锁，则抛出java.lang.IllegalMonitorStateException异常，wait方法调用失败；若有锁，那么当前线程会被添加到对象所关联的**等待集合**中，并释放其持有的监视器对象上的锁。当前线程被阻塞，无法执行，直到被从对象所关联的等待集合中移除，才可继续执行。   

​	由于wait方法的调用需当前线程持有监视器对象上的锁，因此wait方法的调用需放入synchronized声明的方法或代码块中。当执行wait方法时，当前线程已经进入synchronized声明的互斥块中，已经持有所需的锁。**在synchronized方法或代码块中使用的监视器对象必须是wait方法调用的接收者所关联的监视器对象**。   

​	wait方法的等待状态分为：无超时、有超时。  

​	 如果线程处于**有超时**的等待状态，那么线程除了可以被主动唤醒而离开等待状态之外，设定的超时时间过去后也会自动离开等待状态。   

对应的notify和notifyAll用来通知线程离开等待状态

 

### **notify/notifyAll**

 

   对应的notify和notifyAll用来通知线程离开等待状态   

​	调用一个对象的**notify**方法会从该对象关联的**等待集合**中选择一个线程来唤醒。被唤醒的线程可以和其他线程竞争运行的机会。与notify方法相对应的notifyAll方法会唤醒对象关联的等待集合中的所有线程。   

​	Notify方法所唤醒线程的选择由虚拟机实现来决定的，不能保证一个对象所关联的等待集合中的线程按照所期望的顺序被唤醒。很可能一个线程被唤醒后，发现它并不能满足要求，而重新进入等待状态，而真正需要被唤醒的线程却仍处于等待集合中。因此，当等待集合中可能包含多个线程时，一般使用notifyAll方法。不过notifyAll方法会导致线程在没有必要的情况下唤醒，之后又马上进入等待状态，因此会造成一定的性能影响，不过可以保证程序的正确性。   

​	与wait方法相同，notify和notifyAll方法同样需要当前线程拥有方法调用接收者所关联的监视器对象上的锁。当线程被唤醒后，由于在调用wait方法完时，释放了之前所持有的监视器对象上的锁，所以被唤醒的线程需要重新竞争锁来获得继续运行wait方法后的代码的机会。   

​	通常要把wait方法的调用包含在一个循环中，循环条件是线程可以继续执行需要满足的逻辑条件。如果线程继续执行的逻辑条件不满足，那么线程应该再次调用wait方法进入等待状态。   

 

**wait**

```java
public int useWait(final Object lock) {
	try {
		synchronized (lock) {
			// 逻辑条件不满足时，进入等待状态
			while(num == 2){
				lock.wait();
			}
			// 条件满足时
			return ++num;
		}
	} catch (Exception e) {
		e.printStackTrace();
	}
	return num;
}

```

**notify/notifyAll**

```java
public int useNotify(final Object lock){
	synchronized (lock) {
		if (sub == 96) {
			num++; // 除了要用notify唤醒外，还要修改逻辑参数，不然wait方法又会进入等待状态
			lock.notify(); // 只唤醒一个线程
			// lock.notifyAll(); // 全部唤醒	
		     System.out.println("唤醒了 lock....");
		}
		return --sub;
	}
}

```



 





