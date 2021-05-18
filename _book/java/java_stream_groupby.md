### Stream



Java8 API新出的语法，以一种声明式的方式处理数据，类似于SQL，更直观，是对Java集合运算更高级的语法。

可以让处理集合数据更高效、更简洁、更直观，使代码一目了然。



Stream 其实可以看成工厂流水线工作，是把需要处理的数据集合看作一种流，在管道中传输，每个环节是依次完成，经过中间操作处理后，最后由最终操作得到处理后的结果；也有并行流。





### 分组 groupBy



模拟数据，实际中可以是数据库、网络数据、本地文件数据等

~~~json
学生基本信息：
Student(sid=11, name=张三, addr=四川, age=23, height=175)
Student(sid=12, name=李四, addr=四川, age=38, height=168)
Student(sid=13, name=嬴政, addr=北京, age=26, height=185)
Student(sid=14, name=荆轲, addr=湖南, age=17, height=164)
Student(sid=15, name=高渐离, addr=陕西, age=26, height=194)
Student(sid=16, name=盖聂, addr=云南, age=17, height=184)
Student(sid=17, name=刘邦, addr=北京, age=23, height=165)
Student(sid=18, name=王五, addr=四川, age=38, height=165)
Student(sid=19, name=王阳明, addr=四川, age=50, height=174)
~~~

##### 分组求各个地方身高最高的学生

~~~java


// 先分组，然后 求身高最大的 学生
System.out.println("求各个地方最高的人：");
Map<String, Optional<Student>> groupMaxHeightStudentMap = students.stream()
		.collect(Collectors.groupingBy(Student::getAddr, 
		Collectors.mapping(a->a, 
				Collectors.maxBy((a,b)->a.getHeight() - b.getHeight()))));

// 输出结果
groupMaxHeightStudentMap.forEach((k,v)->System.out.println(k + " : " + v.get()));

~~~

结果：

~~~
求各个地方最高的人：
陕西 : Student(sid=15, name=高渐离, addr=陕西, age=26, height=194)
四川 : Student(sid=11, name=张三, addr=四川, age=23, height=175)
湖南 : Student(sid=14, name=荆轲, addr=湖南, age=17, height=164)
云南 : Student(sid=16, name=盖聂, addr=云南, age=17, height=184)
北京 : Student(sid=13, name=嬴政, addr=北京, age=26, height=185)
~~~





