# 这个文件记录一些Linux使用的记录

## cd

cd ~回到当前用户的目录

cd /回到根目录

## ls

ls -l

可以看到程序的读写可执行属性。

ls -a

可以看到隐藏文件

## chmod

改变文件模式

使用ls -l可以看到文件的读写和时候可执行的属性，使用chmod可以进行改变。

**文件的权限描述：**

RWX：读,写,执行 

**分别显示：** 

文件类型 -：一般文件 d：目录

三块区域： 所有者，同组的用户， 别的用户

<img src="pictures/chmod1.png" alt="chmod1" style="zoom: 50%;" />

```c++
chmod 777 filename //可以将文件变为所有人可读可写
```



## source

*参考网址：*https://www.linuxprobe.com/linux-source-useful.html，https://www.linuxcool.com



**`source`命令是一个内置的shell命令，用于从当前shell会话中的文件读取和执行命令。`source`命令通常用于保留、更改当前shell中的环境变量。简而言之，source一个脚本，将会在当前shell中运行execute命令。**

`source`命令可用于：

- 刷新当前的shell环境
- 在当前环境使用source执行Shell脚本
- 从脚本中导入环境中一个Shell函数
- 从另一个Shell脚本中读取变量

**Source命令的语法：**

```shell
#source命令它需要一个文件，如果提供了参数，那么将用作传递脚本的位置参数。
$ source FILENAME [ARGUMENTS]
# 可以使用.代替source命令
$ . FILENAME [ARGUMENTS]
```

### source命令的使用：

#### 1. 刷新当前的shell环境：

**例子：**

可以在当前的shell环境中定义一个别名。为ls -al定义一个别名ll:

```shell
[root@localhost ~]$ echo "alias ll='ls -al'" >> ~/.bashrc 
#在~/.bashrc文件中定义完别名，可以使用source命令刷新当前shell环境：
[root@localhost ~]$ source ~/.bashrc 
#ll就可以当作 ls -al使用啦
```

#### 2. 在当前环境使用`source`执行Shell脚本：

**例子：**

```shell
#创建一个脚本：
[root@localhost ~]$ vim web.sh 
```

```text
#!/bin/bash
echo $website
```

使用`source`在当前shell会话中执行它：

```shell
[root@localhost ~]$ source web.sh 
```

#### 3. 从脚本中导入环境中Shell函数：

**例子：**

创建一个脚本，定义一个函数：

```shell
[root@localhost ~]$ vim func.sh

#!/bin/bash
foo(){
  echo "test function!"
}
```

要在当前的shell会话中导入上述脚本的功能，使用下面命令：

```shell
[root@localhost ~]$ source func.sh 
```

下面在终端中执行以下func.sh脚本中的`foo`函数试试：

```shell
[root@localhost ~]$ foo
test function!
```

#### 4. 从另一个Shell脚本中读取变量

[详情](https://www.linuxprobe.com/linux-source-useful.html)

#### 5. 读取并执行命令

[详情](https://www.linuxprobe.com/linux-source-useful.html)

### 总结：

`source`命令在当前shell中执行脚本，而`exec`命令在新的shell中运行。

### whereis命令：

通过whereis cmake可以找到cmake命令储存的路径。

通过whereis 可以找到shell命令的路径。

### 使用env命令可以显示所有的环境变量



































