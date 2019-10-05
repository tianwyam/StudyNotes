# 学习知识汇总

------






## JAVA

 

### 集合



[java集合-java_collection](https://tianwyam.github.io/StudyNotes/java/java_collection.md)




### **阻塞队列**

 

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

>    使用**通知模式**实现：就是当生产者往满的队列里添加元素时会发生阻塞，然后当消费者消费了队列中的一个元素后，会通知生产者当前队列可用。   
>

 

JDK中**ArrayBlockingQueue**采用的是**Condition**来实现的：

~~~java
    final ReentrantLock lock; // 锁
    private final Condition notEmpty; //空条件 
    private final Condition notFull; //满条件 
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
                notFull.await(); // 若满，则当前线程等待,满条件进行等待
            insert(e); // 插入元素，并且通知 不是空的 ,空条件进行释放
        } finally {
            lock.unlock();
        }
	}

	// 插入元素
	private void insert(E x) {
        items[putIndex] = x;
        putIndex = inc(putIndex);
        ++count;
        notEmpty.signal(); // 通知 当前队列非空，唤醒notEmpty.await()
	}

	public E take() throws InterruptedException {
        final ReentrantLock lock = this.lock;
        lock.lockInterruptibly();
        try {
            while (count == 0)
                notEmpty.await();// 若空，则当前线程等待，空条件进行等待
            return extract();// 获取元素，同时通知 未满，满条件进行释放
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
        notFull.signal();// 通知 当前队列未满，唤醒notFull.await()
        return x;
 	}

~~~

 



------



### **IO**

 

- 字节流：InputStream、OutputStream   
- 字符流：InputStreamReader、OutputStreamWriter   

 

#### **缓冲区**

 

>    **缓冲区**在某些特性下类似与java中的基本类型数组(如字节数组等)，缓存区中的数据排列是线性的，空间也是有限的。
>
> **缓冲区**的3个状态变量：**容器**(capacity)、**读写限制**(limit)和**读写位置**(position)。   

 

>    **容量:** 指的是缓冲区的额定大小，在创建缓冲区时指定的，无法在创建后更改。任何时刻，缓冲区的数据总数都不可超过容量。  
>
>  **读写限制:** 表示是在缓冲区中进行读写操作时的最大允许位置。如对于一个容量为32的缓冲区，如果读写限制是16，则只有前半个缓冲区在读写时可用。如果希望后半个缓冲区可用，则必须把读写限制改为32。   
>
> **读写位置:** 表示当前读写操作时的位置。   
>
> 在任何时候，缓冲区中的各个状态变量之间满足关系：**0<=标记位置<=读写位置<=读写限制<=容器**   

 

>    Java   NIO中的java.nio.**Buffer**类是所有不同数据类型的缓冲区的父类。   
>
> 一些常用方法：  
>
>  向缓冲区**写入**数据，调用**clear**方法，则会修改：读写限制=容器，读写位置=0   
>
> 向缓冲区**读取**数据，调用**flip**方法，则会修改：读写限制=读写位置，读写位置=0   
>
> **重新读取**缓冲区数据，调用**rewind**方法，则会修改：读写位置=0，不会修改读写限制      
>
>  最重要的实现类java.nio.**ByteBuffer**。   

 

代码

  ~~~java
@Test
public void buffer(){

	ByteBuffer buffer = ByteBuffer.allocate(4);
	buffer.putInt(12); // int 占用4个字节
//		buffer.putChar('A'); // 再存放就缓存溢出 BufferOverflowException

	buffer.flip(); //读取缓冲区时调用

	int xx = buffer.getInt();
	System.out.println(xx);
}
  ~~~



 

 







#### **通道**

 

>    **通道**是java NIO对I/O操作提供的一种新的抽象方式。表示为了一个已经建立好的到支持I/O操作的实体的连接。连接一旦建立，就可以在这个连接上进行各种I/O操作。
>
> 在通道上执行的操作类型取决于通道的特性，一般的操作包括数据的读取和写入。   
>
> Java NIO中的通道都实现了java.nio.channels.**Channel**接口。   

 

 

##### **文件通道**

 

   文件是I/O操作的一个常见实体。与文件实体对应的表示文件通道的java.nio.channels.**FileChannel**也是一个强大的通道实现。   

 

**写入文件通道**

~~~java

	@Test
	public void fileChannel(){
		// 定义 文件通道 ，属性：create没有文件则新建，append文件末追加，write可写
		try(FileChannel channel = FileChannel.open(Paths.get("F:\\demo\\a.txt"),
				StandardOpenOption.CREATE,
                  StandardOpenOption.APPEND,
				StandardOpenOption.WRITE);){
			// 定义缓冲区
			ByteBuffer buffer =	ByteBuffer.allocate(64)
				.putChar('A')
				.putChar('G')
				.putChar('E');
			buffer.flip(); // 读取缓冲区时调用
			channel.write(buffer); // 写入文件通道中
		} catch (Exception e) {
			e.printStackTrace();
		}
	}
~~~



 

**文件通道属性选项**

>    **StandardOpenOption**：   
>
> ​	Read：可读  
>
> ​	Write：可写   
>
> ​	Append：文件末追加   
>
> ​	Create：目标文件不存在时，创建新文件   
>
> ​	Create_new：不存在，则创建，存在则报错   
>
> ​	Delete_on_close:创建临时文件，通道关闭是，JVM会尝试删除该文件   

 

 

**文件数据传输**

FileChannel类提供了**transferFrom**和**transferTo**方法用了快速地传输数据。   

**transferFrom**方法把来自一个实现了**ReadableByteChannel**接口的通道中的数据写入文件通道中。   **transferTo**方法把当前文件通道中的数据传输到一个实现了**WriteableByteChannel**接口的通道中。

  

 ~~~java

	@Test
	public void transfer(){
		// 定义 源文件通道 和 目标文件通道
		try(
            FileChannel src = FileChannel.open(Paths.get("F:\\demo\\a.txt"),StandardOpenOption.READ);
		   FileChannel dest = FileChannel.open(Paths.get("F:\\demo\\dest.txt"), 
						StandardOpenOption.CREATE,
						StandardOpenOption.WRITE,
                           StandardOpenOption.APPEND);){
			// 传输数据到 目标文件通道中
			src.transferTo(0, src.size(), dest);
			// 从源文件通道中获取数据 写入文件
			dest.transferFrom(src, 0, Integer.MAX_VALUE);
		} catch (Exception e) {
			e.printStackTrace();
		}
	}
 ~~~



 

##### **内存映射文件**

 

   通过操作系统的内存映射文件支持，可以比较快速地对大文件进行操作。

​	内存映射文件的**原理**在于把系统内存地址映射到操作的文件上。读取这些内存地址就相当于读取文件的内容，而改变这些内存地址的值就相当于修改文件中的内容。通过内存映射的方式对文件进行操作时，不再需要通过I/O操作来完成，而是直接通过内存地址访问操作来完成，这就大大提高了操作文件的性能，因为I/O操作比访问内存地址要慢得多。       

**FileChannel**类的map方法可以把一个文件的全部或部分内容映射到内存中，所得到的是一个ByteBuffer类的子类**MappedByteBuffer**的对象，程序只需要对这个**MappedByteBuffer**类的对象进行操作即可,对其操作会自动同步到文件内容中。可以使用**force**方法进行立即同步到文件中。   

 

###### **映射模式**

   **FileChannel.MapMode**这个枚举类型表示：   

- **READ_ONLY**:只读操作   
- **READ_WRITE**:可读可写   
- **PRIVATE**:对MappedByteBuffer所修改不会同步到文件中，而是同步到一个私有的副本中。   

 ~~~java

	@Test
	public void mappedByteBuffer(){
		// 文件通道
		try(FileChannel channel = FileChannel.open(Paths.get("F:\\demo\\a.txt"), 
				StandardOpenOption.CREATE,
				StandardOpenOption.READ,
				StandardOpenOption.WRITE);){
			// 内存映射文件 READ_WRITE：可读写
			MappedByteBuffer buffer = channel.map(FileChannel.MapMode.READ_WRITE, 0, channel.size());
			byte b = buffer.get();
			buffer.put(7,b);
			// 立即同步到文件中
			buffer.force();
		} catch (Exception e) {
			e.printStackTrace();
		}
	}
 ~~~



 

 

------



### **NIO.2**

 

#### **Path**

 

   **Path**文件路径的抽象。提供了操作路径的很多实用方法   

​	resolve方法：把当前路径当成父目录，把参数当成子目录，解析成一个新目录   

​	resolveSibling方法：跟resolve一样，只是把当前路径的父目录当成解析解析时的父目录   

​	subpath方法：获取当前路径的子路径，参数中的序号表示的是路径中名称元素的序号   

​	startsWith方法：当前路径是否以参数路径开始   

​	endsWith方法：当前路径是否以参数路径结尾   

​	normalize方法：获取到真实位置，去掉一些./,../等冗余路径   

 ~~~java

	@Test
	public void path(){
		Path path1 = Paths.get("a","b"); // a\b
		Path path2 = Paths.get("c","d"); // c\d
		
		Path res = path1.resolve(path2); // a\b\c\d
		Path resv = path1.resolveSibling(path2); // a\c\d
		
		Path subpath = path1.subpath(0, 1); // a
		boolean startsWith = path1.startsWith("a"); // true
		
		Path normalize = Paths.get("./a/../a/b/../c/d").normalize(); // a\c\d
	}

 ~~~



 

#### **Files**

 

   **Files**简化文件操作的工具类。  

​	 Files.**createFile**创建文件   

​	Files.**delete**删除文件   

​	Files.**copy**(source,target)复制文件   

​	Files.**move**(source,target)移动   

 ~~~java
@Test
public void files() throws IOException{
	// 创建文件
	Path path = Files.createFile(Paths.get("file/files.txt").toAbsolutePath());
	// 内容
	List<String> contents = new ArrayList<>();
	contents.add("Hello ");
	contents.add("World");
	// 写文件
	Files.write(path, contents, Charset.forName("UTF-8"));
	Files.size(path);
	// 获取所有的字节数组
	byte[] bytes = Files.readAllBytes(path);
	ByteArrayOutputStream output = new ByteArrayOutputStream();
	Files.copy(path, output);
	// 输出
	System.out.println(new String(bytes));

	Path target = Paths.get("file/newfiles.txt");
	// 权限 文件读写权限
	Set<PosixFilePermission> perms = PosixFilePermissions.fromString("rw-rw-rw-");
	// 文件属性集合
	FileAttribute<Set<PosixFilePermission>> attrs=PosixFilePermissions.asFileAttribute(perms);
	// 创建 有读写权限的 文件
	Files.createFile(target,attrs);
	// 复制 文件的属性一起复制的
	Files.copy(path, target, StandardCopyOption.COPY_ATTRIBUTES);
	// 移动
	Files.move(path, target);
	// 删除
	Files.delete(path);
}

 ~~~







### **异常**

 

   **异常类型：**受检异常、非受检异常。   

**非受检异常**指的是RuntimeException和error类及其子类，所有其他的异常类都称为**受检异常**。   

两种类型的异常在作用上没有什么区别，唯一的差别就在于使用受检异常时的合法性要在编译时刻由编译器来检查。   

**受检异常**它强制要求开发人员在代码中进行显示的声明和捕获，否则会产生编译错误。   

 

 

 

------

 







### **多线程**

 

#### **volatile**

 

   **Volatile**用来对共享变量的访问进行同步。上一次写入操作的结果对下一次读取操作是肯定可见的。   

​	在写入volatile变量值后，CPU缓存中的内容会被写回主存中；在读取volatile变量时，CPU缓存中的对应内容会被置为失效状态，重新从主存中读取。   

​	将变量声明为volatile相当于单个变量的读取和写入添加了同步操作。但是volatile在使用时不需要利用锁机制，因此性能要优于synchronized关键词。   

​	Volatile的主要作用是确保对一个变量的修改被正确地传播到其他线程中。

​	常使用volatile变量的一个场景是把作为循环结束的判断条件的变量声明为volatile。   

 

#### **synchronized**

 

   **Synchronized**是基本的线程同步方式之一。Synchronized可以添加到方法或代码块上。主要用来实现**线程之间的互斥**，即同一时刻只有一个线程允许执行特定的代码，来保证多个线程访问共享变量时的正确性。   

​	声明synchronized的方法或代码块在同一时刻只能有一个线程允许访问的。如果当前已经有线程正在访问synchronized方法或代码块，那么其他试图访问该方法或代码块的线程会处于等待状态。这种互斥性使该方法或代码块中的代码逻辑实际上成为了一个原子操作。   

​	所有的java对象都有一个与之关联的**监视器对象**(**monitor**),允许线程在该监视器对象上进行加锁和解锁操作。每个synchronized关键词在使用时都与一个监视器对象相对应。对于声明为synchronized的方法，**静态方法**对应的监视器对象是所在java类对应的Class类的对象所关联的监视器对象，而**实例方法**使用的是当前对象实例所关联的监视器对象。对于synchronized**代码块**，对应的监视器对象是synchronized代码块声明中的对象所关联的监视器对象。   

​	在一个线程允许执行方法或代码块之前，需先获取对应的监视器对象上的锁。在执行完后，该线程所持有的锁会被自动释放。上一次的解锁操作肯定在下一次的成功加锁操作前发生，由于解锁操作是上一次线程在synchronized方法或代码块中执行的最后一个动作，而加锁是在下一次线程执行synchronized方法或代码块的第一个动作，所以上一次线程运行时对共享变量的修改对下一次线程中的动作是肯定可见的。   

​	当锁被释放时，对共享变量的修改会从CPU缓存中直接写回到主存中；当锁被获取时，CPU缓存中的内容会被置为无效状态，从主存中重新读取共享变量的值。当有线程在执行synchronized方法或代码块时，其他线程由于无法获取锁而处于等待状态，不会影响当前线程的运行。   

 ~~~java
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

 ~~~



 

关键字**synchronized**和**volatile**进行比较：

​	A、  关键字volatile是线程同步的轻量级实现，所以性能会比synchronized要好。   

​	B、  Volatile只能修饰变量，而synchronized可以修饰方法及代码块。   

​	C、  多线程访问volatile不会发生阻塞，而synchronized会发生阻塞。   

​	D、  Volatile能保证数据的可见性，但不能保证原子性；

​		而synchronized可以保证原子性，也可以间接保证可见性，因为它会将私有内存和公有内存中的数据做同步。   

​	E、  Volatile解决的是变量在多线程间的可见性；而synchronized解决的是多个线程间访问资源的同步性。   

 

 

 







#### **Object类的(wait/notify/notifyAll)**

 

   除了synchronized互斥访问外，线程之间也需要通过**协作**的方式来完成任务。   

 

##### **wait**

 

   **wait**方法是在Object类中定义。作用是使当前线程进入等待状态。   

​	**调用wait方法的含义：**java中的每个对象除了有与之关联的**监控器对象**外，还有一个与之关联的**包含线程的等待集合**。在调用wait方法时，该方法调用的接收者所关联的监视器对象是所使用的监视器对象，同时wait方法所影响的是执行wait方法调用的当前线程。成功调用wait方法的先决条件是当前线程获取到监视器对象的锁。若没有锁，则抛出java.lang.IllegalMonitorStateException异常，wait方法调用失败；若有锁，那么当前线程会被添加到对象所关联的**等待集合**中，并释放其持有的监视器对象上的锁。当前线程被阻塞，无法执行，直到被从对象所关联的等待集合中移除，才可继续执行。   

​	由于wait方法的调用需当前线程持有监视器对象上的锁，因此wait方法的调用需放入synchronized声明的方法或代码块中。当执行wait方法时，当前线程已经进入synchronized声明的互斥块中，已经持有所需的锁。**在synchronized方法或代码块中使用的监视器对象必须是wait方法调用的接收者所关联的监视器对象**。   

​	wait方法的等待状态分为：无超时、有超时。  

​	 如果线程处于**有超时**的等待状态，那么线程除了可以被主动唤醒而离开等待状态之外，设定的超时时间过去后也会自动离开等待状态。   

对应的notify和notifyAll用来通知线程离开等待状态

 

##### **notify/notifyAll**

 

   对应的notify和notifyAll用来通知线程离开等待状态   

​	调用一个对象的**notify**方法会从该对象关联的**等待集合**中选择一个线程来唤醒。被唤醒的线程可以和其他线程竞争运行的机会。与notify方法相对应的notifyAll方法会唤醒对象关联的等待集合中的所有线程。   

​	Notify方法所唤醒线程的选择由虚拟机实现来决定的，不能保证一个对象所关联的等待集合中的线程按照所期望的顺序被唤醒。很可能一个线程被唤醒后，发现它并不能满足要求，而重新进入等待状态，而真正需要被唤醒的线程却仍处于等待集合中。因此，当等待集合中可能包含多个线程时，一般使用notifyAll方法。不过notifyAll方法会导致线程在没有必要的情况下唤醒，之后又马上进入等待状态，因此会造成一定的性能影响，不过可以保证程序的正确性。   

​	与wait方法相同，notify和notifyAll方法同样需要当前线程拥有方法调用接收者所关联的监视器对象上的锁。当线程被唤醒后，由于在调用wait方法完时，释放了之前所持有的监视器对象上的锁，所以被唤醒的线程需要重新竞争锁来获得继续运行wait方法后的代码的机会。   

​	通常要把wait方法的调用包含在一个循环中，循环条件是线程可以继续执行需要满足的逻辑条件。如果线程继续执行的逻辑条件不满足，那么线程应该再次调用wait方法进入等待状态。   

 

**wait**

~~~java
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

~~~

**notify/notifyAll**

~~~java

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

~~~



 

#### **Thread**

 

##### **线程状态**

 

   在一个Thread类的对象被创建出来后，它可能处于不同的状态。进行与线程相关的不同操作可能导致Thread类的对象所处的状态有所改变。

不同的线程状态由枚举类型**Thread.State**来表示：   

​	1）  **NEW**:线程刚被创建出来   

​	2）  **RUNNABLE**:线程处于可运行的状态   

​	3）  **BLOCKED**:线程在等待一个监视器对象上的锁时的状态   

​	4）  **WAITING**:调用某些方法会使当前线程进入等待状态   

​	5）  **TIMED_WAITING**:类似WAITING,但是增加了指定的超时时间。时间已过，则退出等待   

​	6）  **TERMINATED**:线程的运行已终止   

 

##### **Join**

 

   Thread类的join方法提供了一种简单的同步方法，允许当前线程等待另一个线程运行结束。   

 

##### **Sleep**

 

   Thread类的静态方法sleep可以让当前线程进入睡眠状态一段时间，线程的代码执行会暂停，但是线程不会释放所持有的锁。因此不要把对sleep的调用放在synchronized方法或代码块内，否则会造成其他等待获取锁的线程长时间处于等待状态。   

 

##### **yield**

 

   若当前线程因某些原因无法继续执行，那么可以使用yield方法来尝试让出所占用的CPU资源，让其他线程获取运行的机会。调用yield方法对操作系统上的调度器来说只是一个信号，但调度器不一定会立即进行线程切换。   

 

 







#### **Lock**

 

   java.util.concurrent.locks.Lock   表示的是一个锁，可以通过其中的lock方法获取锁，然后通过unlock释放锁。   

​	**lock**方法获取锁的方式类似于synchronized，如果调用lock方法无法获取到锁，则线程进行等待。   	

​	**lockInterruptibly**方法获取锁类似于lock方法，允许当前线程在等待锁的过程中被中断。   

​	**tryLock**方法以非阻塞的方式获取锁，如果在调用tryLock方法时无法获取锁，则返回false，不会阻塞当前线程。   

 

**注意：**不要将获取锁的过程写在try块里面，因为如果在获取锁(自定义锁的实现)时发生异常，异常抛出的同时，则会导致锁无故释放。

   ~~~java
@Test
public void lock(){
	Lock lock = new ReentrantLock();
	// 获取锁
	lock.lock(); // 放在try块外面
	try {

	} finally {
		// 释放锁
		lock.unlock();
	}
}
   ~~~



 

 

##### **ReentrantLock**

 

   **ReentrantLock**重入锁，就是支持**重进入**的锁，它表示该锁能够支持一个线程对资源的重复加锁。除此之外，该锁还支持获取锁的公平和非公平性选择。       

​	**重进入**是指任意线程在获取到锁后能够再次获取该锁而不会被锁所阻塞。   

​		1）**线程再次获取锁。**锁需要去识别获取锁的线程是否为当前占据锁的线程，如果是，则再次获取成功。   

​		2）**锁的最终释放。**线程重复N次获取了锁，随后在第N次释放该锁后，其他线程能够获取到该锁。锁最终释放要求：锁对于获取   进行计数自增，计数表示当前锁被重复获取的次数，而锁释放时，则计数自减，当计数等于0时，表示锁已经成功释放。       

​	**公平性**与否是针对获取锁而言的，如果一个锁是公平的，那么锁的获取顺序就应该符合请求时间的绝对时间顺序，即FIFO。   

​	实现则是**ReentrantLock(boolean fair)**，fair=true,则表示该锁是公平性的。   

​	**公平性锁**保证了锁的获取按照了FIFO原则，而代价就是进行了大量的线程切换。

​	**非公平性锁**虽然可能造成线程“饥饿”，但极少的线程切换，保证了其更大的吞吐量。   

 

 

 

##### **ReadWriteLock**

 

   java.util.concurrent.locks.**ReadWriteLock**， 

​	**ReadWriteLock**读写锁接口实际上表示的是两个锁   一个是读取操作的**共享锁**，一个是写入操作使用的**排他锁**。其实现**ReentrantReadWriteLock**。   

​	可以通过ReadWriteLock接口的readLock方法和writeLock方法来获取表示对应的锁的Lock接口的实现对象。

​	在没有线程进行写入操作时，进行读取操作的多个线程都可以获取读取锁；而进行写入操作的线程只有获取到写入锁后才能进行写入操作。多个线程可以同时进行读取操作，但是同一时刻只允许一个线程进行写入操作。   

 

 

#### **Condition**

 

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

   ~~~java
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

   ~~~



 

------





#### **并发工具类**

 

##### **CountDownLatch**

 

   **CountDownLatch**允许一个或多个线程等待其他线程完成操作。可以实现join的功能且比join更加强大。   

​	**CountDownLatch**的构造函数接受一个int类型的等待线程数N，   

​	其中**countDown**方法，可以使N减一，**await**方法会阻塞当前线程，直到N为零。   

 

**CountDownLatch**使用场景(**需等待多个线程完成后，再执行本线程**)

~~~java

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

~~~









##### **CyclicBarrier**

 

   **CyclicBarrier**同步屏障，它要做是事情就是让一组线程到达一个屏障(也可以叫同步)时被阻塞，直到最后一个线程到达屏障时，屏障才会开门，所有被屏障拦截的线程才会继续运行。   

 

 

------





### **JVM**

 

#### **垃圾回收**

 

##### **分代回收方式**

 

   **分代回收**是垃圾回收中的一种常见算法。特点是把内存划分成不同的世代，分别对应虚拟机中存活时间不同的对象。对于存活时间不同的对象，可以采用不同的回收策略。       

​	对于包含存活时间较短的对象的内存空间，其中所包含的存活对象较少，可以被回收的区域比较多，而且状态变化比较快，因此对这个世代的内存进行回收的频率比较高，速度较快。而对于存活时间比较长的对象，回收的频率可以比较低。       

​	在HotSpot虚拟机中，把内存划分为3个世代：**年轻**、**年老**、**永久**。大部分对象所需内存的分配都是在年轻代区域进行的。当垃圾回收器运行时，**年轻代**中的很多对象可能已经不再存活，可以直接被回收。而有些对象可能仍然处于存活状态。某些对象可能经过若干个垃圾回收操作后，仍然处于存活状态，对于这些对象，垃圾回收器会把这些对象移动到**年老代**的内存区域。**永久代**中包含的是java虚拟机自身运行所需的对象。       

​	**年轻代**有划分为：一个伊甸园(**eden)**、两个存活区(**survivor** space)。       

​	大部分内存分配都是在伊甸园(**eden**)中进行的，由于伊甸园的内存空间较小，因此某些所需内存较大的对象无法直接在伊甸园中进行分配，而直接在年老代中进行。   

​	两个存活区(**survivor**   space)中总有一个是空白的，在对年轻代进行垃圾回收时，先把伊甸园中的存活对象复制到当前空白的存活区中，接着对另一个非空白的存活区中的存活对象进行处理。如果对象的存活时间较短，那么同样将其复制到空白的存活区中；如果对象的存活时间较长，则复制到年老代区域。在复制到空白的存活区过程中，如果发现该存活区已经满了，那么将其复制到年老代区域。经过这两次复制后，就可以把伊甸园和非空白的存活区中的内容直接全部清空，因为这两个区域中的对象要么不再存活，要么已复制到其他内存区域中。   

​	在完成垃圾回收后，下一次的内存分配可以继续从空白的伊甸园开始进行，而两个存活区的作用也发生了交换。   

 

对于年老代和永久代内存区域，通常采用的是另一种回收算法: **标记清除压缩算法**。

 

##### **标记清除压缩算法**

 

   **标记清除压缩算法**分为三个步骤：标记(mark)、清除(sweep)、压缩(compact).   

​	第一个步骤的作用是扫描整个内存区域，把当前仍然存活的对象**标记**出来；   

​	第二个步骤的作用则是清理内存区域，**清除**垃圾；   

​	第三个步骤的作用是**压缩**整个内存区域，把存活对象所占的内存都移动到内存区域的起始位置，使内存中可用区域是连续的。   

​	经过压缩后，在年老代和永久代中进行内存分配就变得很容易，只需要从可用区域的开头位置进行分配即可。   

 

 

 

 

------





### **Java7新知识**

 

#### **Switch**

 

   Java中switch支持char\int\byte\short及它们的封装类类型，还有枚举类型。   

​	Java7中额外新增了String类型，这个新特性是编译器这个层次实现的。而在虚拟机和字节代码层次下，还是只支持使用与整数类型兼容的类型。

​	**虽然开发人员在java源代码中使用的是String类型，但是在编译过程中，是将String类型替换成对应的hashcode，而case里面的值也被替换成常量的哈希值，然后再case语句中仍然要使用String的equals方法来进行字符串的判断。**   

 

如: Java源代码

   ~~~java
public static void xx(String key){
		String sigal = "" ;
		switch (key) {
		case "大于":
			sigal = "大于 > " ;
			break;
		case "小于":
			sigal = "小于 > " ;
			break;
		case "等于":
			sigal = "等于 > " ;
			break;
		default:
			sigal = " 错误 " ;
			break;
		}
		System.out.println(sigal);
	}

   ~~~





编译后

  ~~~java
public static void xx(String key) {
      String sigal;
      label23: {
         sigal = "";
         switch(key.hashCode()) {
         case 727623:
            if(key.equals("大于")) {
               sigal = "大于 > ";
               break label23;
            }
            break;
         case 750687:
            if(key.equals("小于")) {
               sigal = "小于 > ";
               break label23;
            }
            break;
         case 998501:
            if(key.equals("等于")) {
               sigal = "等于 > ";
               break label23;
            }
         }
         sigal = " 错误 ";
      }
      System.out.println(sigal);
   }

  ~~~





#### **TWR**

 

   **TWR:**   try-with-resources，能够被try语句所管理的资源需要满足一个条件，就是其资源java类要实现**AutoCloseable**接口，否则会出现编译错误。

当需要释放资源的时候，该接口的close方法会被自动调用。   

 

代码

```java

public void twr(){
	// 可自动关闭
	try(FileInputStream in = new FileInputStream("e:\\fstab");
			FileOutputStream out = new FileOutputStream("e:\\a");){
		byte[] buffer = new byte[1024];
		int len = -1 ;
		while ((len = in.read(buffer)) != -1) {
			out.write(buffer, 0, len);
		}
	} catch (Exception e) {
		e.printStackTrace();
	}
}


```



 

#### **Objects**

 

   在java.util包中新增了一个用来操作对象的工具类java.util.Objects。

**Objects**类中包含的都是静态方法，可进行快速对对象操作。   

​	**compare**方法:两对象比较操作。   

​	**equals**方法:判断两个对象是否相等，若两个都是null，则为true；若一个为空，则为false   

​	**deepEquals**方法:作用于equals相似，若参数是数组，则调用Arrays类的deepEquals进行数组比较   

​	**toString**方法:获取对象字符串形式，若参数为null，则输出null字符串。当传入参数是两个时，如第一个参数为null，则输出第二个参数当作返回值(相当于默认值)   

 

```java

@Test
public void objects(){
	
	// 判断两个对象是否相等，如有一个为null，则为false
	boolean equals = Objects.equals("a", new String("a")); // true
	boolean equals1 = Objects.equals(null, new String("a")); // false
	// 数组比较
	boolean equals2 = Objects.deepEquals(new String[]{"a","b"}, new String[]{"a","b"}); // true
	// 获取对象字符串形式
	String str = Objects.toString(null,"参数为空"); // 输出 “参数为空”
}


```



 

------

 

 

 

 







## **框架**

 

 

### **Spring boot**

 

#### **1、解析JSON数据**

 

使用FastJson转JSON格式

 ~~~java
//第一种方法: 在启动类让其继承WebMvcConfigurerAdapter。
//然后重新实现方法configureMessageConverters。

// 启动类
@SpringBootApplication
public class SpringBootApp extends WebMvcConfigurerAdapter{

    @Override
    public void configureMessageConverters(List<HttpMessageConverter<?>> converters) {
        super.configureMessageConverters(converters);

        // 1、定义fastJson的转换器
        FastJsonHttpMessageConverter converter = new FastJsonHttpMessageConverter();
        // 2、配置fastJson
        FastJsonConfig fastJsonConfig = new FastJsonConfig();
        fastJsonConfig.setSerializerFeatures(
                SerializerFeature.PrettyFormat // 格式化
        );

        // 最后添加到总的转换器中
        converter.setFastJsonConfig(fastJsonConfig);
        converters.add(converter);

    }

    public static void main(String[] args) {
        SpringApplication.run(SpringBootApp.class, args);
    }
}


 ~~~



 ~~~java
//第二种方式: 采用@Bean注解自定义实现


@Configuration
public class CustomFastJsonConfig {

    @Bean
    public HttpMessageConverters fastJsonHttpMessageConverter(){
        // FastJson转换器
        FastJsonHttpMessageConverter converter = new FastJsonHttpMessageConverter();
        FastJsonConfig fastJsonConfig = new FastJsonConfig();

        fastJsonConfig.setSerializerFeatures(
                SerializerFeature.PrettyFormat
        );

        converter.setFastJsonConfig(fastJsonConfig);
        return  new HttpMessageConverters(converter);
    }
}


 ~~~



*FastJson*中JSONField的用法

~~~java

/**
 * FastJson中 JSONField
 *      format 格式转换输出
 *      serialize = false 表示不输出此字段
 */
@JSONField(format = "yyyy-MM-dd HH:mm:ss")
private Date birth ;

@JSONField(serialize = false)
private  String remark ;


~~~



 

 









#### **2、热部署devtools**

 ~~~xml
1、首先添加依赖
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
    <optional>true</optional>
    <scope>true</scope>
</dependency>

2、添加插件
<!--构建节点-->
<build>
    <plugins>
        <!--重新打包 热部署插件 -->
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
            <executions>
                <execution>
                    <goals>
                        <goal>repackage</goal>
                    </goals>
                </execution>
            </executions>
            <configuration>
                <!--fork :  如果没有该项配置则devtools不会起作用，即应用不会restart -->
                <fork>true</fork>
            </configuration>
        </plugin>
    </plugins>
</build>

3、然后修改类文件、配置文件等，应用都会重启。但是它的重启方式比手工重启效率快很多。
其原理是使用的两个ClassLoader加载。一个ClassLoader加载那些不会改变的类(第三方JAR)，另一个ClassLoader加载会改变的类，称为restart ClassLoader。
这样在有代码更改的时候，原来的restart ClassLoader被丢弃，重新创建一个新的restart ClassLoader，由于需要加载的类比较少，所以实现了较快的重启。

4、有可能配置了还是无效，则需配置编译器自动编译。

 ~~~



 

 

#### **3、JPA**

 

   **JPA**全称(java persistence API)，是SUN公司官方提出的**Java持久化规范**。

它为Java开发人员提供了一种对象/关系映射工具来管理Java应用程序中的关系数据。   

**持久化**即把数据(内存中的数据)保存到可永久保存的存储设备中。   

 

 

------




## **数据库**

 

### **序列**

 ~~~sql
  -- Create sequence   
  create sequence MYSQL.SEQ_GUI   
  start with 1   
  increment by 1; 
  
  下个序列：SEQ_GUI.NEXTVAL
  当前序列：SEQ_GUI.CURRVAL
  获取序列：select SEQ_GUI.NEXTVAL FROM dual ; -- oracle
 ~~~

​	

 



------





## **Linux**

 

### **常用命令**

 

#### **目录操作**

 

**mkdir**

**递归创建目录**

   ~~~shell
mkdir –p /home/java/xx   
   ~~~



 

**scp**

**远程复制**

   ~~~
scp 源地址 目的地址 
	如: scp /home (username)@IP:/home    
	scp /home 192.168.1.1:/home   
   ~~~



​       

**rm**

**删除**

  ~~~shell
 rm –rf /home/java  强制删除   
  ~~~



​       

#### **文件操作**

​       

**tail**

**动态显示**

   ~~~shell
tail –f –n 1000 xx.log   
   ~~~



 

#### **文件搜索**

 

**find 查找**

  ~~~shell
 find /etc –name test 在目录etc下按名字test查找              

​	-iname test 不区分大小写              

​	-size +100M 查找大于100M的文件(+n 大于 –n 小于 n 等于)              

​	-user admin 查找所属用户为admin的文件              

​	-cmin -5    查找5分钟内被修改过属性的文件及目录 c   change              

​	-amin -5    查找5分钟内被访问过的文件 a access              

​	-mmin -5    查找5分钟内修改内容的文件 m   modify   
  ~~~



 

**grep 搜索**

 ~~~shell
 grep ‘a’ test.txt 	# 在文件test.txt中搜索匹配a的关键字   

​	grep –A 10 –B 20 keyword   # test.txt 在文件中搜索匹配关键字并输出前20行后10行间的数据   

​	grep –i keyword test.txt  # 忽略大小写搜索匹配   

​	grep –v keywory test.txt  # 排除搜索匹配
 ~~~



 

#### **权限管理**

 

**chmod 修改权限**

  ~~~shell
 chmod –R 777 /home/java  # 递归赋予权限(及目录下所以文件及文件夹都具有权限)   
  ~~~



 

**chown 修改所属者**

  ~~~shell
 chown admin test.txt  # 改变文件test.txt的所属者为admin   
  ~~~



 

**chgrp 改变所属组**

   ~~~shell
chgrp group test.txt  # 改变文件test.txt的所属组为group   
   ~~~



 

#### **用户管理**

 

**su 切换用户**

   ~~~sh
su – admin # 切换到admin用户下   
   ~~~



 

**useradd 添加用户**

   ~~~shell
useradd admin # 新建一个admin用户   
   ~~~



​       

**userdel 添加用户**

   ~~~shell
userdel -r admin # 删除用户的同时删除用户的根目录(/home/admin)   
   ~~~



​       

**passwd 设置密码**

   ~~~shell
passwd admin  # 为admin用户设置密码   
   ~~~



​       

 

#### **压缩解压**

 

**zip 压缩**

  ~~~shell
 zip –r java.zip java   # 将java目录及目录下所以文件压缩成java.zip   
  ~~~



 

**unzip 解压**

   ~~~shell
unzip java.zip –d  ./java      # 将java.zip解压到当前目录下的java文件夹里   
   ~~~



 

**gzip 压缩**



   ~~~shell
gzip -c build.sh > build.sh.gz  # 将文件build.sh打包成build.sh.gz   
   ~~~



 





**gunzip 解压**

   ~~~shell
gunzip build.sh.gz  # 解压   
   ~~~



 

**tar** 

   ~~~shell
tar - czvf 123.tar.gz 123 打包 

​	–c 打包 

​	–z 打包并压缩 

​	–v 显示打包过程 

​	–f 指定文件名   

tar –xzvf 123.tar.gz 解压 

	–x 解包   
   ~~~



 

#### **网络**

 

**telnet查询端口是否调通**

   ~~~shell
telnet IP port   
   ~~~



 

**ping 测试网络连通性**

   ~~~shell
ping –c3 ip  c3次数   
   ~~~



 

**ifconfig 查看/设置网卡信息**

~~~shell
ifconfig  # 查看网卡信息   

ifconfig eth0 192.168.1.100  # 临时设置eth0的ip地址为192.168.1.100   
~~~



 

 

#### 建立信任

 

   实现主机A和主机B间的信任连接步骤：   

​	1）  主机A上使用命令创建密钥：**ssh –keygen –t rsa**   

​	2）  把主机A的/root/.ssh/id_ras.pub文件内容复制到主机B的/root/.ssh/authorized_keys   

​	3）  此时主机A连接主机B就不需密码了   

​	4）  相同的，把主机B公共密钥复制拷贝到主机A的/root/.ssh/authorized_keys下   

​	5）  此时主机A和主机B间就建立起信任了   

 

 

 

 






