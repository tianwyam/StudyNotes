





# **一、常用命令**

 

## **1.1、目录操作**


### **mkdir**

**递归创建目录**

```shell
mkdir –p /home/java/xx   
```



 

### **scp**

**远程复制**

```shell
scp 源地址 目的地址 
	如: scp /home (username)@IP:/home    
	scp /home 192.168.1.1:/home   
```



​       

### **rm**

**删除**

```shell
 rm –rf /home/java  强制删除   
```



​       

## **1.2、文件操作**

​       

**tail**

**动态显示**

```shell
tail –f –n 1000 xx.log   
```



 

### **文件搜索**

 

**find 查找**

```shell
 find /etc –name test 在目录etc下按名字test查找              

​	-iname test 不区分大小写              

​	-size +100M 查找大于100M的文件(+n 大于 –n 小于 n 等于)              

​	-user admin 查找所属用户为admin的文件              

​	-cmin -5    查找5分钟内被修改过属性的文件及目录 c   change              

​	-amin -5    查找5分钟内被访问过的文件 a access              

​	-mmin -5    查找5分钟内修改内容的文件 m   modify   
```



 

**grep 搜索**

```shell
# 在文件test.txt中搜索匹配a的关键字  
grep ‘a’ test.txt 	

# test.txt 在文件中搜索匹配关键字并输出前20行后10行间的数据   
grep –A 10 –B 20 keyword   

# 忽略大小写搜索匹配  
grep –i keyword test.txt  

# 排除搜索匹配
grep –v keywory test.txt  

# 查找日志文件中存在关键词‘错误’的前后10行并且高亮显示
grep -A 10 -B 10 --color '错误' error.log  
```



 

## **1.3、权限管理**

 

### **chmod 修改权限**

```shell
 chmod –R 777 /home/java  # 递归赋予权限(及目录下所以文件及文件夹都具有权限)   
```



 

**chown 修改所属者**

```shell
 chown admin test.txt  # 改变文件test.txt的所属者为admin   
```



 

**chgrp 改变所属组**

```bash
chgrp group test.txt  # 改变文件test.txt的所属组为group   
```



 

## 1.4、**用户管理**

 

**su 切换用户**

```sh
su – admin # 切换到admin用户下   
```



 

**useradd 添加用户**

```shell
useradd admin # 新建一个admin用户   
```



​       

**userdel 添加用户**

```shell
userdel -r admin # 删除用户的同时删除用户的根目录(/home/admin)   
```



​       

**passwd 设置密码**

```shell
passwd admin  # 为admin用户设置密码   
```



​       

 

## **1.5、压缩解压**

 

**zip 压缩**

```shell
 zip –r java.zip java   # 将java目录及目录下所以文件压缩成java.zip   
```



 

**unzip 解压**

```shell
unzip java.zip –d  ./java      # 将java.zip解压到当前目录下的java文件夹里   
```



 

**gzip 压缩**



```shell
gzip -c build.sh > build.sh.gz  # 将文件build.sh打包成build.sh.gz   
```



 





**gunzip 解压**

```shell
gunzip build.sh.gz  # 解压   
```



 

**tar** 

```shell
tar - czvf 123.tar.gz 123 打包 

​	–c 打包 

​	–z 打包并压缩 

​	–v 显示打包过程 

​	–f 指定文件名   

tar –xzvf 123.tar.gz 解压 

	–x 解包   
```



 

## **1.6、网络**

 

##### 查找进程

~~~shell
ps -ef | grep 进程名 | grep -v grep 
## -v 是排除
~~~





**telnet查询端口是否调通**

```shell
telnet IP port   
```



 

**ping 测试网络连通性**

```shell
ping –c3 ip  c3次数   
```



 

**ifconfig 查看/设置网卡信息**

```shell
ifconfig  # 查看网卡信息   

ifconfig eth0 192.168.1.100  # 临时设置eth0的ip地址为192.168.1.100   
```



 

 

## 1.7、建立信任

 

   实现主机A和主机B间的信任连接步骤：   

​	1）  主机A上使用命令创建密钥：**ssh –keygen –t rsa**   

​	2）  把主机A的/root/.ssh/id_ras.pub文件内容复制到主机B的/root/.ssh/authorized_keys   

​	3）  此时主机A连接主机B就不需密码了   

​	4）  相同的，把主机B公共密钥复制拷贝到主机A的/root/.ssh/authorized_keys下   

​	5）  此时主机A和主机B间就建立起信任了   

 

 

 