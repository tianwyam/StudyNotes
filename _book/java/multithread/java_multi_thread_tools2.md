# Java同步工具类



<br>



- CountDownLatch 计数器
- CyclicBarrier 屏障
- Semaphore 信号量



<br><br>



## 1. CountDownLatch 计数器



<br>



CountDownLatch ：允许一个或多个线程等待其他线程完成操作



<br>



```java
/*
 * 同步工具类：CountDownLatch 计数器
 * 内部有个计数器，只有等计数器为0时， latch.await() 才能继续往下走
 * latch.countDown() 就是每一次 计数 减一 
 * 子线程不需要等待，会继续往下执行
 */
```


<br><br>



### 1.1 示例



<br>



示例：学生签到

主线程：模拟老师等待学生签到，只有等所有的学生签完到，老师才能走

子线程：模拟学生签到，签完到，可以走了

<br>





新建一个线程类，模拟学生签到

~~~java

/**
 * @description
 *	模拟 学生 签到 线程
 * @author TianwYam
 * @date 2021年6月30日下午5:30:59
 */
public class SignInThread extends Thread {
	
	private String name;
	private CountDownLatch latch ;
	
	private SignInThread(String name, CountDownLatch latch) {
		super(name);
		this.name = name ;
		this.latch = latch ;
	}
	
	@Override
	public void run() {
		
		System.out.println(name + ": 签完到了");
		latch.countDown();
		System.out.println(name + ": 走了");
		
	}
}

~~~



<br><br>



主线程：

~~~java

/*
 * 同步工具类：CountDownLatch 计数器
 * 内部有个计数器，只有等计数器为0时， latch.await() 才能继续往下走
 * latch.countDown() 就是每一次 计数 减一 
 * 子线程不需要等待，会继续往下执行
 */


public static void main(String[] args) {
	
	// 计数器
	CountDownLatch latch = new CountDownLatch(2);
	
	new SignInThread("学生1", latch).start();
	new SignInThread("学生2", latch).start();
	
	
	try {
		System.out.println("老师等学生签到");
		latch.await();
		System.out.println("所有的学生都签完到了，老师也走了");
	} catch (InterruptedException e) {
		e.printStackTrace();
	}
	
}

~~~



<br><br>



### 1.2 运行效果



<br>



~~~java
老师等学生签到
学生1: 签完到了
学生1: 走了
学生2: 签完到了
学生2: 走了
所有的学生都签完到了，老师也走了
~~~



<br><br>



## 2. CyclicBarrier 屏障



<br>



CyclicBarrier：同步屏障，它要做是事情就是让一组线程到达一个屏障(也可以叫同步)时被阻塞，直到最后一个线程到达屏障时，屏障才会开门，所有被屏障拦截的线程才会继续运行。



<br>



```java
/*
 * 同步工具类：CyclicBarrier 屏障
 * 如同有一个屏障 把所有线程都 隔开到一边，只有所有的线程 等待就绪后，屏障才打开
 * barrier.await() 就是等待就绪，需要等所有的执行了  await 后，才能继续往下走
 * 子线程 需要互相等待，才会继续往下执行
 */
```



<br><br>



### 2.1 示例



<br>



示例：模拟玩游戏

子线程：所有玩家准备好了，才能进入游戏



<br>



新建一个线程类，模拟玩家玩游戏

~~~java

/**
 * @description
 *	模拟玩游戏的 线程
 * @author TianwYam
 * @date 2021年6月30日下午5:30:38
 */
public class PlayGameThread extends Thread {
	
	private String name;
	private CyclicBarrier barrier ;
	
	private PlayGameThread(String name, CyclicBarrier barrier) {
		super(name);
		this.name = name ;
		this.barrier = barrier ;
	}
	
	@Override
	public void run() {
		
		System.out.println(name + ": 准备完毕");
		try {
			barrier.await();
		} catch (InterruptedException | BrokenBarrierException e) {
			e.printStackTrace();
		}
		System.out.println(name + ": 进入游戏");
		
	}
}

~~~



<br>



主线程：

~~~java

/*
 * 同步工具类：CyclicBarrier 屏障
 * 如同有一个屏障 把所有线程都 隔开到一边，只有所有的线程 等待就绪后，屏障才打开
 * barrier.await() 就是等待就绪，需要等所有的执行了  await 后，才能继续往下走
 * 子线程 需要互相等待，才会继续往下执行
 */

public static void main(String[] args) {
	
	// 屏障 模拟 3个玩家玩游戏
	// CyclicBarrier barrier = new CyclicBarrier(3);
	
	// CyclicBarrier(int parties, Runnable barrierAction)
	// 当所有的线程都达到屏障后，优先执行 barrierAction
	CyclicBarrier barrier = new CyclicBarrier(3, ()->{
		System.out.println("所有玩家已准备好，开始玩游戏");
	});
	
	System.out.println("等所有玩家都准备好了，才能开始游戏：");
	
	new PlayGameThread("玩家1", barrier).start();
	new PlayGameThread("玩家2", barrier).start();
	new PlayGameThread("玩家3", barrier).start();
	
}

~~~



<br><br>



### 2.2 运行结果



<br>



~~~java
等所有玩家都准备好了，才能开始游戏：
玩家1: 准备完毕
玩家2: 准备完毕
玩家3: 准备完毕
所有玩家已准备好，开始玩游戏
玩家3: 进入游戏
玩家1: 进入游戏
玩家2: 进入游戏
~~~



<br><br>





## 3. Semaphore 信号量



<br>



Semaphore 信号量，只有拿到了信号量，才能执行，执行完后，归还信号量，没有拿到需要等待



<br>



```java
/*
 * 同步工具类：Semaphore 信号量
 * 定义总的信号量，拿到了，才能执行，执行完后，归还信号量，没有拿到需要等待
 * semaphore.tryAcquire() 尝试获取 信号量
 * semaphore.release() 释放信号量
 * 可用于流量控制、削峰
 */
```



<br><br>



### 3.1 示例



<br>



示例：学生抢饭卡，抢到了才能去吃饭，没有抢到的，只有等待

子线程：学生抢饭卡，吃完归还饭卡



<br>



新建一个子线程类：模拟学生抢饭卡

~~~java

/**
 * @description
 *	模拟一个吃饭线程
 * @author TianwYam
 * @date 2021年7月1日上午9:19:31
 */
public static class EatFoodThread extends Thread {
	
	private String name ;
	
	private Semaphore semaphore ;
	
	private EatFoodThread(String name, Semaphore semaphore) {
		this.name = name ;
		this.semaphore = semaphore ;
	}
	
	
	@Override
	public void run() {
		
		while (true) {
			
			// 尝试 抢 饭卡
			if (semaphore.tryAcquire()) {
				System.out.println(name + "：抢 到饭卡了");
				System.out.println(name + "：吃饭 ");
				
				try {
					// 吃饭
					Thread.sleep(3);
				} catch (InterruptedException e) {
					e.printStackTrace();
				}
				
				System.out.println(name + "：吃完饭了，归还饭卡 ");
				semaphore.release();
				break ;
			} else {
				System.out.println(name + "：没有抢到饭卡，需要等待 或者 下次再来 ");
				try {
					// 等饭卡
					Thread.sleep(1);
				} catch (InterruptedException e) {
					e.printStackTrace();
				}
			}
		}
		
	}
}


~~~



<br>



主线程：

~~~java

/*
 * 同步工具类：Semaphore 信号量
 * 定义总的信号量，拿到了，才能执行，执行完后，归还信号量，没有拿到需要等待
 * semaphore.tryAcquire() 尝试获取 信号量
 * semaphore.release() 释放信号量
 * 可用于流量控制、削峰
 */

public static void main(String[] args) {
	
	Semaphore semaphore = new Semaphore(2);
	
	for (int i = 1; i < 4; i++) {
		new EatFoodThread("学生" + i, semaphore).start();
	}
	
	
}

~~~



<br><br>



### 3.2 运行结果

<br>



~~~java
学生1：抢 到饭卡了
学生2：抢 到饭卡了
学生2：吃饭 
学生1：吃饭 
学生3：没有抢到饭卡，需要等待 或者 下次再来 
学生3：没有抢到饭卡，需要等待 或者 下次再来 
学生3：没有抢到饭卡，需要等待 或者 下次再来 
学生3：没有抢到饭卡，需要等待 或者 下次再来 
学生2：吃完饭了，归还饭卡 
学生1：吃完饭了，归还饭卡 
学生3：抢 到饭卡了
学生3：吃饭 
学生3：吃完饭了，归还饭卡 

~~~





<br><br>



























