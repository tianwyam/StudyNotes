# Java序列化



[TOC]





序列化：Java中一种机制，序列化（serialization）就是把对象的状态信息转换成可以存储或传输的形式的过程，一般写入IO流中，以二进制形式流传。

反序列化则是将序列化好了的对象写入文件后，可以从文件中读取出来，并且对它进行反序列化，也就是说，对象的类型信息、对象的数据，还有对象中的数据类型可以用来在内存中新建对象。



意义：序列化机制允许将实现序列化的Java对象转换位字节序列，这些字节序列可以保存在磁盘上，或通过网络传输，以达到以后恢复成原来的对象。序列化机制使得对象可以脱离程序的运行而独立存在。



**所有需要保存到磁盘的java对象都必须是可序列化的。**



Java提供的两种实现方式：

需要序列化的对象

- 实现接口：java.io.Serializable
- 实现接口：java.io.Externalizable





## 方式一：Serializable





Java中提供了 类 ObjectInputStream 和 ObjectOutputStream 数据流，它们包含反序列化和序列化对象的方法



### 工具类



~~~java


/**
 * @Description: 
 * 序列化 对象
 * @author: TianwYam
 * @date 2021年5月17日 下午8:48:05
 * @param object
 * @return
 */
public static byte[] serialize(Object object) {
	
	try ( ByteArrayOutputStream out = new ByteArrayOutputStream();
			ObjectOutputStream writer = new ObjectOutputStream(out) ; ){
		writer.writeObject(object);
		return out.toByteArray();
	} catch (Exception e) {
		e.printStackTrace();
	}
	
	return new byte[0] ;
}



/**
 * @Description: 
 * 反序列化 
 * @author: TianwYam
 * @date 2021年5月17日 下午8:52:05
 * @param obj
 * @return
 */
public static Object unserialize(byte[] obj) {
	
	try ( ByteArrayInputStream input = new ByteArrayInputStream(obj);
			ObjectInputStream reader = new ObjectInputStream(input); ){
		return reader.readObject();
	} catch (Exception e) {
		e.printStackTrace();
	}
	
	return null ;
}

~~~



### 待序列化对象实现接口：Serializable



~~~java
/**
 * @Description: 
 * 序列化对象，方式一：实现接口 java.io.Serializable
 * @author: TianwYam
 * @date 2021年5月17日 下午8:53:44
 */
@Data
@Builder
public class Person implements Serializable{
	
	private static final long serialVersionUID = -5227964019276779512L;

	private int pid ;
	
	private String name ;
	
	private String addr ;

	@Override
	public String toString() {
		return "Person [pid=" + pid + ", name=" + name + ", addr=" + addr + "]";
	}

}
~~~





### 操作

~~~java
// 序列化对象
Person person = Person.builder().pid(10).name("大卫").addr("成都").build();

// 序列化
byte[] serialize = serialize(person);
System.out.println(new String(serialize));

// 反序列化
Object obj = unserialize(serialize);
System.out.println(obj);

~~~









## 方式二：Externalizable









### 待序列化对象实现接口：Externalizable



实现方法：

- void writeExternal(ObjectOutput out) ;
- void readExternal(ObjectInput in) ;



~~~java

/**
 * @Description: 
 * 序列化对象 方式二：实现接口
 * @author: TianwYam
 * @date 2021年5月17日 下午9:23:14
 */
@Data
@Builder
public class People implements Externalizable{
	

	private int pid ;
	
	private String name ;
	
	private String addr ;
	
	@Override
	public void writeExternal(ObjectOutput out) throws IOException {
		// 可以自定义 写入对象
		out.writeObject(this.pid);
		out.writeObject(this.name);
		out.writeObject(this.addr);
		System.out.println("方式二：");
		System.out.println("序列化");
	}

	@Override
	public void readExternal(ObjectInput in) throws IOException, ClassNotFoundException {
		// 读出对象
		System.out.println("方式二：");
		System.out.println("反序列化");
		this.pid = (Integer)in.readObject();
		this.name = (String)in.readObject();
		this.addr = (String)in.readObject();
	}

	@Override
	public String toString() {
		return "People [pid=" + pid + ", name=" + name + ", addr=" + addr + "]";
	}
	
	

	public People(int pid, String name, String addr) {
		this.pid = pid;
		this.name = name;
		this.addr = addr;
	}

	// 必须要无参构造函数
	public People() { }

}

~~~





### 操作

~~~java
//	方式二：
People people = People.builder().pid(11).name("张三").addr("成都").build();

// 序列化
byte[] serialize = serialize(people);
System.out.println(new String(serialize));

// 反序列化
Object obj = unserialize(serialize);
// People [pid=11, name=张三, addr=成都]
System.out.println(obj);
~~~





## 两种方式对比



| 实现Serializable接口                                         | 实现Externalizable接口   |
| :----------------------------------------------------------- | :----------------------- |
| 系统自动存储必要的信息                                       | 程序员决定存储哪些信息   |
| Java内建支持，易于实现，只需要实现该接口即可，无需任何代码支持 | 必须实现接口内的两个方法 |
| 性能略差                                                     | 性能略好                 |





