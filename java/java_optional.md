

## Optional



[TOC]



Java 8 新语法

是一个可以承载null的一个容器，一定程度上，可以防止空指针异常，使其对 对象判断是否为null，更加方便，简洁。

对比而言，optional可以避免大量的if/else判空处理，并且搭配**Lambda**表达式一起使用，显得更加简洁。



~~~java
java.util.Optional
~~~

### 常用方法

~~~java

// 创建一个容器，不能为空，否则报空指针异常
public static <T> Optional<T> of(T value);
	
// 创建一个容器，可以包含null
public static <T> Optional<T> ofNullable(T value);

// 取容器里面的值
public T get();

// 判断容器中是否存在对象
public boolean isPresent();

// 如果容器中存在对象，则执行 consumer
public void ifPresent(Consumer<? super T> consumer);

// 筛选 
public Optional<T> filter(Predicate<? super T> predicate);

// 取容器对象中的属性
public<U> Optional<U> map(Function<? super T, ? extends U> mapper);

// 如果对象存在，则输出，对象不存在，则返回 other
public T orElse(T other);

// 对象存在，则输出；对象不存在，则执行other，并返回
public T orElseGet(Supplier<? extends T> other);

// 若是对象不存在，则抛出异常
public <X extends Throwable> T orElseThrow(Supplier<? extends X> exceptionSupplier) ;

~~~



### 实例



创建一个实体对象，做演示

~~~java
/**
 * @Description: 
 * 教室 实体类
 * @author: TianwYam
 * @date 2021年4月14日 下午8:43:20
 */
@Data
@Builder
@NoArgsConstructor
@AllArgsConstructor
public class ClassRoom {
	// 教室编号
	private int roomId ;
	// 教室名称
	private String roomName;
	// 班主任
	private Teacher teacher ;
	// 班级学生
	private List<Student> students ;

	@Override
	public String toString() {
		return JSON.toJSONString(this, true);
	}

}
~~~



#### of

创建一个Optional实例，承载着对象

~~~java

// 创建对象
ClassRoom classroom = ClassRoom.builder()
    .roomId(100)
    .roomName("高一一班")
    .students(students)
    .build();
// of 创建一个Optional实例，如容器
System.out.println(Optional.of(classroom));
~~~

输出：

~~~json
Optional[{
	"roomId":100,
	"roomName":"高一一班",
	"students":[
		{
			"addr":"四川",
			"age":23,
			"height":175,
			"name":"张三",
			"sid":11
		},
		{
			"addr":"四川",
			"age":38,
			"height":168,
			"name":"李四",
			"sid":12
		}
	]
}]
~~~



但是不能存放null

如：

~~~java
// 定义一个空对象
ClassRoom room = null ;

// NullPointerException
Optional<ClassRoom> nullOptional = Optional.of(room);
~~~

输出：

~~~java
Exception in thread "main" java.lang.NullPointerException
	at java.util.Objects.requireNonNull(Objects.java:203)
	at java.util.Optional.<init>(Optional.java:96)
	at java.util.Optional.of(Optional.java:108)
	at com.tianya.java.Java8Optional.main(Java8Optional.java:28)
~~~





#### ofNullable

创建一个Optional实例，承载着对象，但是可以存放null对象

~~~java
// 创建一个null对象
ClassRoom room = null ;
Optional<ClassRoom> roomOptional = Optional.ofNullable(room);
// Optional.empty
System.out.println(roomOptional);
~~~

所以，我们常常使用ofNullable来创建Optional，一定程度下可以防止空指针异常





#### isPresent

判断对象是否为null

~~~java

// 创建一个null对象
ClassRoom room = null ;
Optional<ClassRoom> roomOptional = Optional.ofNullable(room);
// roomOptional=null false
System.out.println(roomOptional.isPresent());


// 创建一个不为空的对象
ClassRoom classroom = ClassRoom.builder()
    .roomId(100)
    .roomName("高一一班")
    .students(students)
    .build();

// 创建Optional实例
Optional<ClassRoom> classroomOptional = Optional.ofNullable(classroom);
		
// true
System.out.println(classroomOptional.isPresent());
~~~



#### map

取对象的属性

~~~java
// 获取教室名称
// 若是roomName不为空，则输出roomName
// 若是roomName为空，则输出 “教室名称”
String roomName = classroomOptional.map(ClassRoom::getRoomName)
    .orElse("教室名称");

// 高一一班
System.out.println(roomName);
~~~

若是不使用optional，则：

~~~java
// map -> orElse 类似如下操作
if (classroom != null) {
	String name = classroom.getRoomName();
	if(StringUtils.isNotBlank(name)) {
		System.out.println(name);
	}else {
		System.out.println("教室名称");
	}
}
~~~

对比而言，optional避免了大量的if/else判断，并且搭配**Lambda**表达式一起使用，显得更加简洁





比如：

~~~java
// 先获取教室里的班主任
// 然后获取班主任的名字
// 若是不存在班主任，则输出提示
// 若是存在，则输出班主任姓名
String teachName = classroomOptional.map(ClassRoom::getTeacher)
		.map(Teacher::getTeachName)
		.orElse("教室班主任不存在");

// 教室班主任不存在
System.out.println(teachName);
~~~

不用进行多次if/else进行判空处理

~~~java

if(classroom != null) {
	Teacher teacher = classroom.getTeacher();
	if (teacher != null) {
		String teachName = teacher.getTeachName();
		if (StringUtils.isNotBlank(teachName)) {
			System.out.println(teachName);
		}else {
			System.out.println("教室班主任姓名为空");
		}
	}else {
		System.out.println("教室班主任不存在");
	}
}

~~~







