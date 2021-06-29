

## XML中特殊符号的转译





场景：spring.xml、mapper.xml

有时候使用mybatis项目中，mapper.xml中在编写sql时出现 > < & 等，有时候会有冲突，所有需要转译



![XML特殊字符转译表](xml_translation.png)





或者使用：

<![DATA[

​	// 里面可以写任何 > < & 等符号，但是不能出现   ]]>

]]>

