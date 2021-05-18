### Stream



Java8 API新出的语法，以一种声明式的方式处理数据，类似于SQL，更直观，是对Java集合运算更高级的语法。

可以让处理集合数据更高效、更简洁、更直观，使代码一目了然。



Stream 其实可以看成工厂流水线工作，是把需要处理的数据集合看作一种流，在管道中传输，每个环节是依次完成，经过中间操作处理后，最后由最终操作得到处理后的结果；也有并行流。



实际操作一下：

模拟数据，实际中可以是数据库、网络数据、本地文件数据等

~~~json
学生基本信息：
Student(sid=11, name=张三, addr=四川, age=23, height=175)
Student(sid=12, name=李四, addr=四川, age=38, height=168)
Student(sid=13, name=嬴政, addr=北京, age=26, height=185)
Student(sid=14, name=荆轲, addr=湖南, age=17, height=164)
Student(sid=15, name=曹操, addr=陕西, age=26, height=194)
Student(sid=16, name=盖聂, addr=云南, age=17, height=184)
Student(sid=17, name=刘邦, addr=北京, age=23, height=165)
Student(sid=18, name=王五, addr=四川, age=38, height=165)
Student(sid=19, name=刘备, addr=四川, age=50, height=174)
~~~



#### 过滤 filter

查询大于25岁的学生

~~~java
System.out.println("查询大于 25岁的 学生：");
List<Student> collect = students.stream()
		.filter(student->student.getAge() > 25)
		.collect(Collectors.toList());
collect.forEach(System.out::println);
~~~

结果：

~~~json
Student(sid=12, name=李四, addr=四川, age=38, height=168)
Student(sid=13, name=嬴政, addr=北京, age=26, height=185)
Student(sid=15, name=曹操, addr=陕西, age=26, height=194)
Student(sid=18, name=王五, addr=四川, age=38, height=165)
Student(sid=19, name=刘备, addr=四川, age=50, height=174)
~~~



#### 排序 sorted

按照年龄排序(从小到大)：

~~~java

System.out.println("按照年龄排序(从小到大)：");
List<Student> sortList = students.stream()
		.sorted( (a,b)-> a.getAge() - b.getAge())
		.collect(Collectors.toList());
sortList.forEach(System.out::println);
~~~

结果：

~~~json
Student(sid=14, name=荆轲, addr=湖南, age=17, height=164)
Student(sid=16, name=盖聂, addr=云南, age=17, height=184)
Student(sid=11, name=张三, addr=四川, age=23, height=175)
Student(sid=17, name=刘邦, addr=北京, age=23, height=165)
Student(sid=13, name=嬴政, addr=北京, age=26, height=185)
Student(sid=15, name=曹操, addr=陕西, age=26, height=194)
Student(sid=12, name=李四, addr=四川, age=38, height=168)
Student(sid=18, name=王五, addr=四川, age=38, height=165)
Student(sid=19, name=刘备, addr=四川, age=50, height=174)
~~~



#### 求最大 max

获取最高的学生：

~~~java
System.out.println("获取最高的学生：");
Student maxHeightStudent = students.stream()
		.max((a,b)->a.getHeight() - b.getHeight())
		.get();
System.out.println(maxHeightStudent);
~~~

结果：

~~~json
获取最高的学生：
Student(sid=15, name=曹操, addr=陕西, age=26, height=194)
~~~



#### 求和 reduce

获取所有学生的年龄和

~~~java
Integer ageSum = students.stream()
	.reduce(0, 
			(a,b)-> a + b.getAge(), 
			(a,b)-> a + b);
System.out.println("所有学生的年龄总和：" + ageSum);
~~~

结果：

~~~json
所有学生的年龄总和：258
~~~

类似：

~~~java
Integer allStudentAge = students.stream()
		.map(Student::getAge)
		.reduce(0, Integer::sum);
System.out.println("获取所有人年龄之和：" + allStudentAge);
~~~

结果：

~~~json
获取所有人年龄之和：258
~~~





#### list转Map 



数组集合list转Map

~~~java
Map<Integer, String> mapList = students.stream()
		.collect(
				Collectors.toMap(Student::getSid, 
						Student::getName));
mapList.forEach((k,v)->System.out.println(k + ": " + v));
~~~

结果：

~~~json
16: 盖聂
17: 刘邦
18: 王五
19: 刘备
11: 张三
12: 李四
13: 嬴政
14: 荆轲
15: 曹操
~~~



类似：查询同一个地方的学生，根据学生地址分组

~~~java
System.out.println("同一个地方的人：");
Map<String, List<Student>> addrMap = students.stream()
		.collect(Collectors.groupingBy(Student::getAddr));
System.out.println(JSON.toJSONString(addrMap, 
		SerializerFeature.PrettyFormat));
~~~

结果：

~~~json
{
	"陕西":[
		{
			"addr":"陕西",
			"age":26,
			"height":194,
			"name":"曹操",
			"sid":15
		}
	],
	"四川":[
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
		},
		{
			"addr":"四川",
			"age":38,
			"height":165,
			"name":"王五",
			"sid":18
		},
		{
			"addr":"四川",
			"age":50,
			"height":174,
			"name":"刘备",
			"sid":19
		}
	],
	"湖南":[
		{
			"addr":"湖南",
			"age":17,
			"height":164,
			"name":"荆轲",
			"sid":14
		}
	],
	"云南":[
		{
			"addr":"云南",
			"age":17,
			"height":184,
			"name":"盖聂",
			"sid":16
		}
	],
	"北京":[
		{
			"addr":"北京",
			"age":26,
			"height":185,
			"name":"嬴政",
			"sid":13
		},
		{
			"addr":"北京",
			"age":23,
			"height":165,
			"name":"刘邦",
			"sid":17
		}
	]
}
~~~







#### 分组 groupingBy

根据 地方分组，求出 每个 地方最高的 身高

~~~java
Map<String, Optional<Integer>> groupByList = students.stream()
		.collect(Collectors.groupingBy(Student::getAddr, 
				Collectors.mapping(Student::getHeight, 
				Collectors.maxBy((a,b)->a-b))));

groupByList.forEach((k,v)->System.out.println(k + " : " + v.get()));
~~~

结果：

~~~json
陕西 : 194
四川 : 175
湖南 : 164
云南 : 184
北京 : 185
~~~



还可以：

求各个地方最高的人：

~~~java
System.out.println("求各个地方最高的人：");
Map<String, Optional<Student>> groupMaxHeightStudentMap = students.stream()
		.collect(Collectors.groupingBy(Student::getAddr, 
		Collectors.mapping(a->a, 
				Collectors.maxBy((a,b)->a.getHeight() - b.getHeight()))));


groupMaxHeightStudentMap.forEach((k,v)->System.out.println(k + " : " + v.get()));
~~~

结果：

~~~json
陕西 : Student(sid=15, name=曹操, addr=陕西, age=26, height=194)
四川 : Student(sid=11, name=张三, addr=四川, age=23, height=175)
湖南 : Student(sid=14, name=荆轲, addr=湖南, age=17, height=164)
云南 : Student(sid=16, name=盖聂, addr=云南, age=17, height=184)
北京 : Student(sid=13, name=嬴政, addr=北京, age=26, height=185)
~~~























