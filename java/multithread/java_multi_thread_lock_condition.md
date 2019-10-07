





## **Condition**

 

   任意一个java对象，都拥有一组监视器方法(定义在Object上),主要包括wait/notify/notifyAll，这些方法与synchronized同步关键字配合，可以实现等待/通知模式。

​	**Condition**接口也提供了类似Object的监视器方法，与lock配合也可以实现等待/通知模式。   

 

**Object**和**condition**的监视器方法比较

| **对比项**                                 | **Object**         | **Condition**                                                |
| ------------------------------------------ | ------------------ | ------------------------------------------------------------ |
| 前置条件                                   | 获取对象的锁       | 调用lock.lock()获取锁   调用lock.newCondition()获取Condition对象 |
| 等待                                       | object.wait()      | condition.await()                                            |
| 通知                                       | object.notify()    | condition.signal()                                           |
| 通知所有                                   | object.notifyAll() | condition.signalAll()                                        |
| 等待队列个数                               | 一个               | 多个                                                         |
| 当前线程释放锁并进入等待状态               | 支持               | 支持                                                         |
| 当前线程释放锁并进入等待状态，不响应中断   | 不支持             | 支持                                                         |
| 当前线程释放锁并进入等待状态到将来某个时间 | 不支持             | 支持                                                         |

 

**注意：在使用condition方法前必须获取锁lock。**

```java
public class ConditionUseCase {
     
	private Lock lock = new ReentrantLock();// 锁
	private Condition condition = lock.newCondition(); // 获取 condition
	
	// 等待
	public void await(){
		lock.lock(); // 获取锁
		try {
			System.out.println("进入等待状态。。。。");
			condition.await(); // 等待
			System.out.println("从等待状态中 被唤醒。。。。，则说明获取到了condition相应的锁");
		} catch (Exception e) {
			e.printStackTrace();
		} finally {
			lock.unlock();// 释放锁
		}
	}
	
	// 通知
	public void signal(){
		
		lock.lock(); // 获取锁
		try {
			condition.signal(); // 通知condition上等待的线程
			System.out.println("唤醒condition等待的线程。。。。");
		} catch (Exception e) {
			e.printStackTrace();
		} finally {
			lock.unlock(); // 释放锁
		}
	}
}

```



 