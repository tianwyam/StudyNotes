

## Windows



### 1.常用命令



#### 查找端口


```powershell
#查找端口8005进程
netstat -ano | findstr 8005
```


#### 杀死进程
```powershell
#杀死进程
taskkill /pid 5164 /f
```

#### 查看磁盘大小


```powershell
df -h path
# path是查看路径，查看当前目录，则直接是 df -h 
```

#### 查看当前目录每个文件夹的情况


```powershell
du --max-depth=1 -h path
# 或者
du -sh path
```









## 2.git 操作

[git教程： https://git-scm.com/book/zh/v2](https://git-scm.com/book/zh/v2)



### 设置github 中的SSH-KEY

	为什么要配置这个呢？
	因为你提交代码肯定要拥有你的github权限才可以，但是直接使用用户名和密码太不安全了，所以我们使用ssh key来解决本地和服务器的连接问题。
	
	以下操作皆可以在git-bash.exe中执行



#### 1、检查本机已存在的ssh密钥

~~~
cd ~/. ssh #检查本机已存在的ssh密钥
~~~


#### 2、生成文件
~~~
ssh-keygen -t rsa -C "邮件地址"
~~~

然后连续3次回车，最终会生成一个文件在用户目录下，打开用户目录，找到.ssh\id_rsa.pub文件，记事本打开并复制里面的内容，打开你的github主页，进入个人设置 -> SSH and GPG keys -> New SSH key

#### 3、检测是否配置成功

~~~
ssh -T git@github.com # 注意邮箱地址不用改
~~~

如果提示Are you sure you want to continue connecting (yes/no)?，输入yes
~~~
Hi 你的用户名! You've successfully authenticated, but GitHub does not provide shell access.
~~~
看到这个信息说明SSH已配置成功！


#### 4、配置全局用户名及邮箱

~~~
git config --global user.name "liuxianan"// 你的github用户名，非昵称
git config --global user.email  "xxx@qq.com"// 填写你的github注册邮箱
~~~

