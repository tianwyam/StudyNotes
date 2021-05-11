

### IO



- 字节流：InputStream、OutputStream   
- 字符流：InputStreamReader、OutputStreamWriter   

 

#### **缓冲区**

 

​	**缓冲区**在某些特性下类似与java中的基本类型数组(如字节数组等)，缓存区中的数据排列是线性的，空间也是有限的。

​	**缓冲区**的3个状态变量：**容器**(capacity)、**读写限制**(limit)和**读写位置**(position)。   

 

​	**容量:** 指的是缓冲区的额定大小，在创建缓冲区时指定的，无法在创建后更改。任何时刻，缓冲区的数据总数都不可超过容量。  

​	**读写限制:** 表示是在缓冲区中进行读写操作时的最大允许位置。如对于一个容量为32的缓冲区，如果读写限制是16，则只有前半个缓冲区在读写时可用。如果希望后半个缓冲区可用，则必须把读写限制改为32。   

​	**读写位置:** 表示当前读写操作时的位置。   

​	在任何时候，缓冲区中的各个状态变量之间满足关系：**0<=标记位置<=读写位置<=读写限制<=容器**   

 

​	Java   NIO中的java.nio.**Buffer**类是所有不同数据类型的缓冲区的父类。   

​	一些常用方法：  

​		向缓冲区**写入**数据，调用**clear**方法，则会修改：读写限制=容器，读写位置=0   

​		向缓冲区**读取**数据，调用**flip**方法，则会修改：读写限制=读写位置，读写位置=0   

​		**重新读取**缓冲区数据，调用**rewind**方法，则会修改：读写位置=0，不会修改读写限制      

​		最重要的实现类java.nio.**ByteBuffer**。   

 

代码

```java
@Test
public void buffer(){

	ByteBuffer buffer = ByteBuffer.allocate(4);
	buffer.putInt(12); // int 占用4个字节
//		buffer.putChar('A'); // 再存放就缓存溢出 BufferOverflowException

	buffer.flip(); //读取缓冲区时调用

	int xx = buffer.getInt();
	System.out.println(xx);
}
```



 

 







#### **通道**

 

​	**通道**是java NIO对I/O操作提供的一种新的抽象方式。表示为了一个已经建立好的到支持I/O操作的实体的连接。连接一旦建立，就可以在这个连接上进行各种I/O操作。

​	在通道上执行的操作类型取决于通道的特性，一般的操作包括数据的读取和写入。   

​	Java NIO中的通道都实现了java.nio.channels.**Channel**接口。   

 

 

##### **文件通道**

 

   文件是I/O操作的一个常见实体。与文件实体对应的表示文件通道的java.nio.channels.**FileChannel**也是一个强大的通道实现。   

 

**写入文件通道**

```java
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
```



 

**文件通道属性选项**

​	**StandardOpenOption**：   

​		Read：可读  

​		Write：可写   

​		Append：文件末追加   

​		Create：目标文件不存在时，创建新文件   

​		Create_new：不存在，则创建，存在则报错   

​		Delete_on_close:创建临时文件，通道关闭是，JVM会尝试删除该文件   

 

 

**文件数据传输**

​	FileChannel类提供了**transferFrom**和**transferTo**方法用了快速地传输数据。   

​	**transferFrom**方法把来自一个实现了**ReadableByteChannel**接口的通道中的数据写入文件通道中。   

​	**transferTo**方法把当前文件通道中的数据传输到一个实现了**WriteableByteChannel**接口的通道中。

  

```java
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
```



 

##### **内存映射文件**

 

   通过操作系统的内存映射文件支持，可以比较快速地对大文件进行操作。

​	内存映射文件的**原理**在于把系统内存地址映射到操作的文件上。读取这些内存地址就相当于读取文件的内容，而改变这些内存地址的值就相当于修改文件中的内容。通过内存映射的方式对文件进行操作时，不再需要通过I/O操作来完成，而是直接通过内存地址访问操作来完成，这就大大提高了操作文件的性能，因为I/O操作比访问内存地址要慢得多。       

**FileChannel**类的map方法可以把一个文件的全部或部分内容映射到内存中，所得到的是一个ByteBuffer类的子类**MappedByteBuffer**的对象，程序只需要对这个**MappedByteBuffer**类的对象进行操作即可,对其操作会自动同步到文件内容中。可以使用**force**方法进行立即同步到文件中。   

 

###### **映射模式**

   **FileChannel.MapMode**这个枚举类型表示：   

- **READ_ONLY**:只读操作   
- **READ_WRITE**:可读可写   
- **PRIVATE**:对MappedByteBuffer所修改不会同步到文件中，而是同步到一个私有的副本中。   

```java
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
```



 

 

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

```java
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

```



 

#### **Files**

 

   **Files**简化文件操作的工具类。  

​	 Files.**createFile**创建文件   

​	Files.**delete**删除文件   

​	Files.**copy**(source,target)复制文件   

​	Files.**move**(source,target)移动   

```java
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

```









