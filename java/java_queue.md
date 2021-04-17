## 队列



### 阻塞队列



   **阻塞队列**(**BlockingQueue**)是一个支持如下两个附加操作的队列。   

- **A、支持阻塞的插入方法：**意思是当队列满时，队列会阻塞插入元素的线程，直到队列不满   
- **B、支持阻塞的移除方法：**意思是在队列为空时，获取元素的线程会等待，直到队列为空   

 

#### **常用方法**

| **满/空时** | **抛出异常** | **返回特殊值** | **一直阻塞** | **超时退出**       |
| ----------- | ------------ | -------------- | ------------ | ------------------ |
| 插入方法    | add(e)       | offer(e)       | put(e)       | offer(e,time,unit) |
| 移除方法    | remove()     | poll()         | take()       | poll(time,unit)    |
| 检查方法    | element()    | peek()         |              |                    |

 

#### **常用队列**

​	**ArrayBlockingQueue**：一个由数组结构组成的有界阻塞队列。   

​	**LinkedBlockingQueue**：一个由链表结构组成的有界阻塞队列。  

​	**PriorityBlockingQueue**：一个支持优先级排序的无界阻塞队列。   

​	**DelayQueue**：一个使用优先级队列实现的无界阻塞队列。  

​	**SynchronousQueue**：一个不存储元素的阻塞队列。  

​	 **LinkedTransferQueue**：一个由链表结构组成的无界阻塞队列。  

​	 **LinkedBlockingDeque**：一个由链表结构组成的双向阻塞队列。   

 

 

#### **实现原理**

​	

​	使用**通知模式**实现：就是当生产者往满的队列里添加元素时会发生阻塞，然后当消费者消费了队列中的一个元素后，会通知生产者当前队列可用。   

 

JDK中**ArrayBlockingQueue**采用的是**Condition**来实现的：

```java

// 锁
final ReentrantLock lock; 
//空条件
private final Condition notEmpty;  
//满条件 
private final Condition notFull; 

// 构造函数 初始化锁 及 满/空条件
public ArrayBlockingQueue(int capacity, boolean fair) {
    if (capacity <= 0)
        throw new IllegalArgumentException();
    this.items = new Object[capacity];
    lock = new ReentrantLock(fair);
    notEmpty = lock.newCondition();
    notFull =  lock.newCondition();
}

// 添加元素
public void put(E e) throws InterruptedException {
    checkNotNull(e);
    final ReentrantLock lock = this.lock;
    lock.lockInterruptibly();
    try {
        while (count == items.length)
            // 若满，则当前线程等待,满条件进行等待
            notFull.await(); 
        // 插入元素，并且通知 不是空的 ,空条件进行释放
        insert(e); 
    } finally {
        lock.unlock();
    }
}

// 插入元素
private void insert(E x) {
    items[putIndex] = x;
    putIndex = inc(putIndex);
    ++count;
    // 通知 当前队列非空，唤醒notEmpty.await()
    notEmpty.signal(); 
}

public E take() throws InterruptedException {
    final ReentrantLock lock = this.lock;
    lock.lockInterruptibly();
    try {
        while (count == 0)
            // 若空，则当前线程等待，空条件进行等待
            notEmpty.await();
        // 获取元素，同时通知 未满，满条件进行释放
        return extract();
    } finally {
        lock.unlock();
    }
}

// 获取元素
private E extract() {
    final Object[] items = this.items;
    E x = this.<E>cast(items[takeIndex]);
    items[takeIndex] = null;
    takeIndex = inc(takeIndex);
    --count;
    // 通知 当前队列未满，唤醒notFull.await()
    notFull.signal();
    return x;
}

```

 

