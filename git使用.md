# 这个文档是对于git使用的一些整理



## git 版本回退：

*参考网址：*[(9条消息) git进行版本回退（文件恢复、恢复误提交、已提交）_git回退提交_木鸟飞的博客-CSDN博客](https://blog.csdn.net/qing040513/article/details/109150075)

2.git的4个区
在介绍如何进行git版本回退前，我们需要先了解下git中的4个区：

* 工作区（Working Area） 就相当于我们的工作空间的目录，我们代码本地存放的地方。
* 暂存区（Stage）也称作Index，用来跟踪已暂存的文件，一般是存在.git下的index文件，所以有时也称暂存区为索引。
* 本地仓库（Local Repository）
* 远程仓库（Remote Repository）
















`git reset`











## gitignore

今天在文件夹里面写了一天的记录，不想上传到github上，所以想着，就写一个git ignore文件来忽略掉我private的文件夹吧。自己vim随便写了一下，结果文件没有被忽略，还是被传上去了。谔谔，这里整理一下，gitignore文件应该怎么写。

*参考网址：*[(9条消息) 【Git提交忽略文件】.gitignore语法规则详细说明（可以忽略exe文件、.DS_Store文件、无后缀名的Unix可执行文件）_gitignore忽略.history\_捡起一束光的博客-CSDN博客](https://blog.csdn.net/qq_43827595/article/details/100048727)

# 语法规则

`.gitignore`的语法如下：

- 所有空行或者以 ＃ 开头的行都会被 Git 忽略

- ! 表示不忽略

- ？表示任意的一个字符

- *表示任意数目的字符

- [abc]表示a,b,c中任意字符即可

- 匹配模式可以以（/）**开头**只匹配**当前目录下的匹配文件夹**，可以防止递归

- 匹配模式可以以（/）**结尾**表示当前**匹配是一个目录**

	- 说明
		.gitignore这个文件里的代码是从上往下执行的
		如果你前面说要忽略所有文件*，后面又说不忽略所有文件!*，那么最终的效果是不忽略所有文件，也就是说后面的命令会覆盖签名的命令。
	- 如果是写/Entertainment/，**最前面的/表示只匹配当前目录（gitignore的当前路径下目录）**，别的子目录的Entertainment文件夹并没有被忽略。（只忽略gitignore路径下的Entertainment文件夹中文件）
	- 如果是`Entertainment/`，则忽略根目录中所有满足含Entertainment的文件夹（包括子目录）

	

	```git
	#忽略test文件夹及文件夹下文件
	test
	
	#忽略以.exe结尾的文件
	*.exe
	
	#忽略*.a和*.b文件 
	
	 *.[ab]
	
	#忽略*.b和*.B文件，code.b除外
	
	*.[bB]
	
	!code.b
	
	
	#忽略test文件和test目录
	
	test
	
	#忽略所有test目录
	test/
	
	#只忽略test文件，不忽略test目录
	
	test
	
	!test/
	
	#只忽略当前目录下的test文件和目录，子目录的test不在忽略范围内
	
	/test
	
	
	```




