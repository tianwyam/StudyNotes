

### **Java7新知识**







#### **Switch**

 

   Java中switch支持char\int\byte\short及它们的封装类类型，还有枚举类型。   

​	Java7中额外新增了String类型，这个新特性是编译器这个层次实现的。而在虚拟机和字节代码层次下，还是只支持使用与整数类型兼容的类型。

​	**虽然开发人员在java源代码中使用的是String类型，但是在编译过程中，是将String类型替换成对应的hashcode，而case里面的值也被替换成常量的哈希值，然后再case语句中仍然要使用String的equals方法来进行字符串的判断。**   

 

如: Java源代码

```java
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

```





编译后

```java
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

```





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



 





