### maven引入依赖时识别不到本地jar包问题



解决：本地仓库存在jar包，maven引入依赖时却报错，提示找不到jar包问题



#### 一、本地环境问题



1.maven安装路径中 配置文件：conf/settings.xml 修改成自己本地仓库地址

~~~xml
<localRepository>E:\\repository</localRepository>
~~~



2.IDE中查看maven配置的仓库地址是否是与本地仓库地址一致



#### 二、仓库问题



可能是由于仓库是从另一个地方拷贝过来(如外网拷贝到内网)，仓库里存在多版本临时文件信息，或者是因为网络问题，导致jar描述不全等



**解决：**

删除生成的多余的文件：

~~~
_remote.repositories
m2e-lastUpdated.properties
*.lastUpdated
~~~



可以编写代码批量删除文件如下：

~~~java
// 删除maven仓库版本问题
public class MavenDeleteVersion {
	
	public static void main(String[] args) {
		
        // 本地仓库地址
		String repoPath = "E:\\MavenRepository\\repository" ;
        // 需要删除的文件 后缀
		String[] extensions = new String[] {
				"lastUpdated",
				"properties",
				"repositories",
		} ;
		// 取所有文件 进行删除
		Collection<File> listFiles = FileUtils.listFiles(new File(repoPath), extensions, true);
		for (File file : listFiles) {
			System.out.println("删除文件：" + file.getAbsolutePath());
			FileUtils.deleteQuietly(file);
		}
		System.out.println("总共删除文件数量：" + listFiles.size());

	}

}

~~~



然后在项目中 重新拉取一下maven依赖：

如：eclipse中

~~~
项目 ---> 右键 ---> maven ---> update Project....
~~~



若是最终还是不行，则删除整个jar仓库，重新从maven中央仓库拉取

