

### 集合



![java collection](https://github.com/tianwyam/StudyNotes/blob/master/java/java_collection.jpeg)



#### **Map**



##### **HashMap**

> **HashMap**线程不安全，在多线程环境下，使用HashMap进行put操作会引起死循环，导致CPU利用率接近100%，是因为多线程会导致HashMap的Entry链表形成环形数据结构，一旦形成环形数据结构，Entry的next节点永远不为空，就会产生死循环获取Entry。   

##### **HashTable**

> **Hashtable**容器使用**synchronized**来保证线程安全，所以在线程竞争激烈的环境下效率非常低下。因为当一个线程访问HashTable的同步方法，其他线程也访问HashTable的同步方法时，会进入阻塞或轮询状态。如线程A访问put进行元素添加时，其他线程不但不能使用put方法，也不能使用get方法获取元素。   

 

| HashMap/HashTable区别 |                           HashMap                            |                HashTable                 |
| :-------------------: | :----------------------------------------------------------: | :--------------------------------------: |
|       线程安全        |                          非线程安全                          |                 线程安全                 |
|        NULL值         |                    key和value都可以为null                    |             不允许key为null              |
|       继承关系        |                       继承AbstractMap                        |              继承Dictionary              |
|      容器初始值       |                              16                              |                    11                    |
|       扩容方式        |                          capacity*2                          |               capacity*2+1               |
|       底层结构        |                          数组+链表                           |                数组+链表                 |
|       哈希算法        | 对key的hashcode进行二次hash，以获取更好的散列值，然后对数组长度取模 | 直接使用key的hashcode,然后对数组长度取模 |





##### **ConcurrentHashMap**

 

> **ConcurrentHashMap**所采用的是**锁分段技术**：首先将数据分成一段一段地存储，然后给每一段数据配一把锁，当一个线程占用锁访问其中一个段数据的时候，其他段数据也能被其他线程访问，这样可有效的提升并发访问率。   
>
> **ConcurrentHashMap**是由**Segment**数组结构和**HashEntry**数组结构组成。
>
> **Segment**是一种可重入锁(ReentrantLock),在**ConcurrentHashMap**中扮演锁的角色，HashEntry则用了存储键值对数据。
>
> 一个***ConcurrentHashMap***里包含一个Segment数组。
>
> **Segment**的结构和hashmap类似，是一种数组和链表结构。一个Segment里包含一个HashEntry数组，每个HashEntry是一个链表结构的元素，每一个Segment守护着一个HashEntry数组里的元素，当对HashEntry数组的数据进行修改时，首先必须获取它对应的Segment锁。   

 

 

