

### Java实现FTP文件上传下载



在Java中使用FTP技术实现上传文件到文件服务器或者从文件服务器上拉取文件到本地



采用 apache commons-net工具包实现

maven pom.xml导入依赖

~~~xml
<!-- apache commons-net 工具包  -->
<dependency>
	<groupId>commons-net</groupId>
	<artifactId>commons-net</artifactId>
	<version>3.6</version>
</dependency>
~~~









#### FTP文件上传

上传文件到FTP文件服务器上

~~~java

/**
 * @Description: 上传文件到FTP服务器
 * @author TianwYam
 * @date: 2021年3月30日 
 * @param filePath 需要上传的文件路径
 * @param fileName 需要上传的文件名称
 */
public static boolean sendFile2FTP(String filePath, String fileName) {
    // 新建 FTP连接
    FTPClient ftpClient = new FTPClient();
    try(
        // 读取需要上传的文件
        FileInputStream fis = new FileInputStream(new File(filePath + File.separator + fileName));){

        // 连接FTP服务器
        ftpClient.connect(FTP_SERVER_IP, FTP_SERVER_PORT);
        // 登陆FTP服务器
        ftpClient.login(FTP_USERNAME, FTP_PASSWORD);
        // 设置上传目录(FTP文件服务器上的存放文件的目录)
        ftpClient.changeWorkingDirectory(FTP_FILE_PATH);
        // 设置缓存大小
        ftpClient.setBufferSize(1024);
        // 设置编码
        ftpClient.setControlEncoding("UTF-8");
        // 设置上传文件格式 二进制格式
        ftpClient.setFileType(FTPClient.BINARY_FILE_TYPE);
        // 上传文件、存储文件到FTP服务器
        ftpClient.storeFile(fileName, fis);
        // 上传成功
        return true;
    }catch (Exception e) {
        log.error("上传文件【{}】到FTP服务器发生异常:", fileName,  e);
    }finally {
        try {
            if(ftpClient != null){
                ftpClient.disconnect();
            }
        } catch (IOException e) {
            log.error("关闭FTP服务器连接发生异常", e);
        }
    }

    return false;
}


~~~





#### FTP文件下载

从FTP文件服务器中下载同后缀类型的文件到本地，并返回下载的文件路径

~~~java

/**
 * @Description: 从FTP服务器中获取文件
 * @author TianwYam
 * @date: 2021年3月30日 
 * @param filePath 获取后的文件存放目录
 * @param remoteFileSuffix FTP服务器中的文件后缀，返回同类型文件
 * @return 存放返回的文件路径
 */
public static List<String> getFileFromFTP(String filePath, String remoteFileSuffix) {
	
	// 存放返回的文件路径
	List<String> retFilePathList =  new ArrayList<>();
	// 新建 FTP 连接
	FTPClient ftpClient = new FTPClient();
	try {
		// 创建目录(本地存放文件的路径，若是不存在，则创建)
		mkdir(filePath);
		
		// 连接FTP服务器
		ftpClient.connect(FTP_SERVER_IP, FTP_SERVER_PORT);
		// 登陆FTP服务器
		ftpClient.login(FTP_USERNAME, FTP_PASSWORD);
		
		// 设置缓存大小
		ftpClient.setBufferSize(1024);
		// 设置编码
		ftpClient.setControlEncoding("UTF-8");
		// 设置文件格式 二进制格式
		ftpClient.setFileType(FTPClient.BINARY_FILE_TYPE);
		
		// 切换到 远程下载目录文件下(FTP文件服务器上的存放文件的目录)
		ftpClient.changeWorkingDirectory(FTP_FILE_PATH);
		
		// 获取 FTP文件服务器上的 文件列表
		FTPFile[] listFiles = ftpClient.listFiles(FTP_FILE_PATH, new FTPFileFilter() {
			@Override
			public boolean accept(FTPFile ftpFile) {
				// 文件名
				String name = ftpFile.getName();
				// 后缀
				String suffix = FilenameUtils.getExtension(name);
                // 获取相同类型的文件
				return Objects.equals(suffix, remoteFileSuffix) ;
			}
		});
		
		// 下载多个文件
		for (FTPFile ftpFile : listFiles) {
			String name = ftpFile.getName();
            // 保存到本地的 文件全路径
			String outFilePath = filePath + File.separator + name;
			log.info("从FTP服务器下载文件：{} 到：{}", name, outFilePath);
			try(
				// 获取后的文件存放目录
				FileOutputStream fos = new FileOutputStream(new File(outFilePath));){
				// 从FTP服务器中获取 文件 到输出流
				ftpClient.retrieveFile(name, fos);
				// 从FTP服务器中删除文件
				ftpClient.deleteFile(name);
				// 刷新输出缓冲
				fos.flush();
				// 把文件路径返回
				retFilePathList.add(outFilePath);
			}catch (Exception e) {
				log.error("从FTP服务器中获取文件【{}】发生异常:", name,  e);
			}
		}
		
	}catch (Exception e) {
		log.error("从FTP服务器中获取文件类型【{}】发生异常:", remoteFileSuffix,  e);
	}finally {
		try {
            if(ftpClient != null){
            	ftpClient.disconnect();
            }
		} catch (IOException e) {
			log.error("关闭FTP服务器连接发生异常", e);
		}
	}
	
	return retFilePathList ;
}
	
~~~





