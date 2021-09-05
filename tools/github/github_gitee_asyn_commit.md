

# 本地项目同时提交到GitHub和码云gitee上管理



<br>

由于GitHub很慢，码云gitee相对于比较快，所以有时候就需要：本地项目同时提交到GitHub和码云gitee上管理

<br>



## 管理多个仓库



<br>

有时候会遇到 码云gitee 或者 GitHub上面已经有项目了，想在另一个仓库也管理起来的情况

<br>

第一步：首先克隆远程仓库已有的项目到本地（若是本地已经有了，则跳过这一步）

~~~bash

# 克隆项目到本地（这儿拿我的一个项目 spring-boot-learn 做演示）
git clone https://github.com/tianwyam/spring-boot-learn.git
~~~



<br>



第二步：本地项目空间可以查看远程仓库有哪些

~~~bash

# 查看远程仓库 列表
git remote -v
~~~

<br>

~~~bash

$ git remote -v

origin  https://github.com/tianwyam/spring-boot-learn.git (fetch)
origin  https://github.com/tianwyam/spring-boot-learn.git (push)

~~~

此时可以看出，本地项目远程仓库只有 GitHub



<br>



第三步：在gitee码云上新建仓库，用来管理此本地项目的（去官网创建仓库）

第四步：本地项目添加gitee码云的远程仓库链接

~~~bash

## 添加 远程仓库 mayun
git remote add mayun https://gitee.com/tianwyam/spring-boot-learn.git
~~~



<br>



第五步：查看本地项目的远程仓库列表

~~~bash

$ git remote -v
mayun   https://gitee.com/tianwyam/spring-boot-learn.git (fetch)
mayun   https://gitee.com/tianwyam/spring-boot-learn.git (push)
origin  https://github.com/tianwyam/spring-boot-learn.git (fetch)
origin  https://github.com/tianwyam/spring-boot-learn.git (push)

~~~

此时多了一个 mayun 远程仓库，自此本地项目就可以想码云gitee 和 GitHub上面同时提交代码管理此项目

<br>



第六步：分别推送到各自远程仓库上

~~~bash

# 本地项目 修改或添加文件后 提交到暂存区后

# 分别推送到各自远程仓库上
# 推送到 GitHub
git push origin master 

# 推送到 gitee码云上面
git push mayun master
~~~

git commit 后，执行不同推送命令到远程仓库上



<br>





