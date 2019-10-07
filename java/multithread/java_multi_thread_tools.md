



## **并发工具类**

 

### **CountDownLatch**

 

   **CountDownLatch**允许一个或多个线程等待其他线程完成操作。可以实现join的功能且比join更加强大。   

​	**CountDownLatch**的构造函数接受一个int类型的等待线程数N，   

​	其中**countDown**方法，可以使N减一，**await**方法会阻塞当前线程，直到N为零。   

 

**CountDownLatch**使用场景(**需等待多个线程完成后，再执行本线程**)

```java
public class CountDownLatchUseCase {
	
	private final static int THREAD_COUNT = 2 ; // 总共的线程数
	
	public static void main(String[] args) throws InterruptedException {
		// 定义的将要等待的线程个数
		final CountDownLatch latch = new CountDownLatch(THREAD_COUNT);
		
		new Thread(new Runnable() {
			public void run() {
				System.out.println(Thread.currentThread() + " 完成了 " + System.currentTimeMillis());
				latch.countDown(); // 已经完成了的 线程数 减一
			}
		},"A").start();
		
		new Thread(new Runnable() {
			public void run() {
				System.out.println(Thread.currentThread() + " 完成了 " + System.currentTimeMillis());
				latch.countDown(); // 已经完成了的 线程数 减一
			}
		},"B").start();
		
		latch.await(); // 让当前线程等待 阻塞，直到所有 THREAD_COUNT 线程完成后
		System.out.println(Thread.currentThread()+" 所有的线程都完成了");
	}
}

```









### **CyclicBarrier**

 

   **CyclicBarrier**同步屏障，它要做是事情就是让一组线程到达一个屏障(也可以叫同步)时被阻塞，直到最后一个线程到达屏障时，屏障才会开门，所有被屏障拦截的线程才会继续运行。   

 

 