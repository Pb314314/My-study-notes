# CSAPP的学习笔记

# CMU课程内容整理：

从第11个lecture开始学，谔谔 自己自学随性一点捏：》



## lecture Template

**内容概括：**😊



### 知识点1

**内容：**

* 
* 

### 知识点2

**内容：**

* 
* 

### 总结：



## lecture 11： The Memory Hierarchy

**内容概括：**😊

这节课是关于计算机中的储存器的介绍。介绍了多种储存器，包含了RAM中的SRAM和DRAM。然后介绍了disk的构造和取数据，也介绍了SSD这种速度更快的外部存储。

然后讲解了Locality，这个很有意思。最终讲了Memory Hierarchy，以及它能实现更高速的原理。

### RAM

<img src="pictures/image-20230417113621073.png" alt="image-20230417113621073" style="zoom: 50%;" />

**RAM分为两种：**

* SRAM(Static RAM)（更快，价格高，在芯片上，作为内存的cache ）访问数据需要1clock cycle。
* DRAM(Dynamic RAM)（作为内存）访问数据需要10clock cycle。

两种RAM都是易失性的(volatile memory)，也就是没电的时候会失去内容。

<img src="pictures/image-20230417113933494.png" alt="image-20230417113933494" style="zoom:50%;" />

刚刚说了RAM是易失性的，这里介绍的是**非易失性**的，例如ROM，flash。

* ROM(只读存储器)，**只能读不能写**。例如存储固件Firmware。
* Flash（闪存）。可以擦除重写。例如disk（磁片），SSD（固态）。

<img src="pictures/image-20230417115123908.png" alt="image-20230417115123908" style="zoom:50%;" />

这张图是关于CPU和memory的连接。中间的I/O bridge将系统总线和内存总线连接到I/O总线。（I/O Bridge是芯片 要做一些处理，将系统总线的电子信号转化为内存总线的电子信号）

**CPU从main memory中读取数据，disk或者ssd中的数据必须要搬运到main memory中才能被使用！main memory是计算机的必要组成部分**

### Disk

<img src="pictures/image-20230417142659414.png" alt="image-20230417142659414" style="zoom:50%;" />

机械硬盘通过机械的方式来访问数据，也就是将磁盘一层层叠起来，转动。有针头访问。因为是机械的转动方式，读取速度比较慢。

读取的速度是毫秒级别的。disk controller来将地址翻译成机械的运动，来查找数据。

<img src="pictures/image-20230417143001779.png" alt="image-20230417143001779" style="zoom: 50%;" />

可以看出从disk中访问数据真的很慢很慢！！

### I/O Bus

<img src="pictures/image-20230417143112466.png" alt="image-20230417143112466" style="zoom:50%;" />

**peripheral的数据通过I/O Bus传输给CPU和main memory**

**example,cpu读取disk数据:**

<img src="pictures/image-20230417144133620.png" alt="image-20230417144133620" style="zoom:50%;" />

CPU通过系统总线发送指令给Disk Controller

<img src="pictures/image-20230417144225829.png" alt="image-20230417144225829" style="zoom:50%;" />

Disk Controller从Disk中找到数据传输到Main memory中。

**I/O设备可以自己执行读和写，不需要CPU的参与。这个过程称为：DMA(Direct Memory Access)(传输数据的时候CPU在处理别的操作)**

<img src="pictures/image-20230417144442552.png" alt="image-20230417144442552" style="zoom:50%;" />

DMP传输完成后， Disk Controller会向CPU发送中断信号（PIC (Programmable Interrupt Controller)）通知CPU完成啦！

### Solid State Disks (SSDs)

<img src="pictures/image-20230417144653376.png" alt="image-20230417144653376" style="zoom:50%;" />

SSD和disk最大的不同在于SSD通过硬件/固件进行数据的读取，也就是图中的Flash translation layer。这样速度会更快！

<img src="pictures/image-20230417144829090.png" alt="image-20230417144829090" style="zoom:50%;" />

* read比write快
* Sequential的R/W都比Random的R/W要快。

SSD比起Disk更加容易磨损，但是更快！

<img src="pictures/image-20230417144930291.png" alt="image-20230417144930291" style="zoom:50%;" />

<img src="pictures/image-20230417145125484.png" alt="image-20230417145125484" style="zoom:50%;" />

从这张图的发展可以看出CPU和memory以及disk的速度gap很大。所以我们该怎么更好地解决IO和数据读取的问题呢？引出了Locality的概念！

### Locality

![image-20230417145043146](pictures/image-20230417145043146.png)

由于CPU和Memory以及disk的数据读取gap，好的程序需要满足较好的locality的要求。

**Temporal locality:**

最近被访问的内容未来很有可能会被再次访问。

**Spatial locality：**

最近访问的内容边上的内容很有机会被访问。

<img src="pictures/image-20230417145706978.png" alt="image-20230417145706978" style="zoom:50%;" />

所以未来满足locality，访问二维数组的时候，应该先遍历行再遍历列。也就是stride1的遍历。

### Caching in the memory hierarchy

<img src="pictures/image-20230417145830350.png" alt="image-20230417145830350" style="zoom:50%;" />

<img src="pictures/image-20230417145848962.png" alt="image-20230417145848962" style="zoom:50%;" />

这个是memory hierarchy，越上面的部分空间越小，访问速度越快。

cache指的是再上层的空间缓存下层的内容，这样可以被更快的访问。因此，满足了locality的代码，当内容被搬运到更上层的位置，且更有机会被访问，就让速度更快啦！

<img src="pictures/image-20230417150240347.png" alt="image-20230417150240347" style="zoom:50%;" />

<img src="pictures/image-20230417150253305.png" alt="image-20230417150253305" style="zoom:50%;" />

<img src="pictures/image-20230417150305220.png" alt="image-20230417150305220" style="zoom:50%;" />

这就是几个概念，找内容从cache找到了hit，没找到是miss。然后下面出现miss的情况。

<img src="pictures/image-20230417150429746.png" alt="image-20230417150429746" style="zoom:50%;" />

### 总结：

<img src="pictures/image-20230417150410773.png" alt="image-20230417150410773" style="zoom:50%;" />



## lecture 13： Linking

**内容概括：**😊

这节课是关于程序写完之后到运行的中间过程。



### 静态链接

![image-20230418173344289](pictures/image-20230418173344289.png)

**可重定义文件：**

不可被搬入内存，不能运行

**可执行文件：**

可以被搬入内存，可以运行

**可重定位文件(Relocatable object file)的生成过程：**

* 图中的Translators包含三个步骤，也就是cpp,cc1,as
* cpp是预处理器，将源文件翻译成ASCII中间文件
* cc1将中间文件翻译成汇编文件
* as将汇编文件翻译成可重定位文件

**通过可重定位文件生成可执行文件：**

* 多个可执行文件通过Linker生成可执行文件

<img src="pictures/image-20230418174718325.png" alt="image-20230418174718325" style="zoom: 50%;" />

<img src="pictures/image-20230418175308674.png" alt="image-20230418175308674" style="zoom:50%;" />

使用链接可以让程序作为多个文件，更加清晰，可以建库

时间效率高，改动文件只要重新编译改动的文件

节省空间

### Linker做的事情

<img src="pictures/image-20230418175742957.png" alt="image-20230418175742957" style="zoom:50%;" />

将所有**全局变量**和**函数**的定义和引用关联起来。

**全局变量和函数是存在在ELF文件的。注意：local variable是程序运行时记录在栈上的，Linker不会管他们。**

<img src="pictures/image-20230418180057143.png" alt="image-20230418180057143" style="zoom:50%;" />

将不同的code（程序指令）和data（定义的数据（函数和全局变量））分别合并在一个section中。

**每一个.o文件，也就是重定位文件也是ELF文件的格式。Linker将每个ELF格式文件相同类型的section合并。**

<img src="pictures/image-20230418180501839.png" alt="image-20230418180501839" style="zoom:50%;" />

<img src="pictures/image-20230418180538734.png" alt="image-20230418180538734" style="zoom:50%;" />

ELF文件时这三种文件的统一的文件格式。这个第三种Share object files指的是后面会说的库，也就是动态库和静态库。

<img src="pictures/image-20230418180750227.png" alt="image-20230418180750227" style="zoom:50%;" />

<img src="pictures/image-20230418180804379.png" alt="image-20230418180804379" style="zoom:50%;" />

这个是ELF文件的section定义。

<img src="pictures/image-20230418181103044.png" alt="image-20230418181103044" style="zoom:50%;" />

文件中Linker Symbols分为三类：

* 全局变量：在自己文件内定义，可以被别的文件访问的全局变量。也就是**non-static的functions和variables**
* 外部变量：从别的文件referenced的变量，应该指的是别的文件中的全局变量
* 本地变量：**这个指的是不能被外部访问的全局变量，也就是被static修饰的variables**

**这里说的本地变量不是存放在栈上的变量！**

<img src="pictures/image-20230418181557321.png" alt="image-20230418181557321" style="zoom:50%;" />

<img src="pictures/image-20230418181737069.png" alt="image-20230418181737069" style="zoom:50%;" />

像这里的static修饰的函数中的变量，就要存放在ELF的.data section中。尽管名字一样，都会存放。在symbol table中也有各自独立的entry。

<img src="pictures/image-20230418182014634.png" alt="image-20230418182014634" style="zoom:50%;" />

这些全局变量如果名字重复了会用这个方法来选择使用哪个。谔谔 但是最好不要重复。

<img src="pictures/image-20230418235207314.png" alt="image-20230418235207314" style="zoom:50%;" />

所以全局变量是很烦的，很容易互相干扰，因为不能名字重复。为了避免这种相互干扰：

* 使用Static，来限制使用空间
* 在定义时赋值（避免别的复制的strong symbol影响）
* 使用extern如果使用的是别的module的全局变量



<img src="pictures/image-20230418235247609.png" alt="image-20230418235247609" style="zoom:50%;" />

### 重定位Relocation

<img src="pictures/image-20230418235459845.png" alt="image-20230418235459845" style="zoom:50%;" />

将多个可重定位文件的相同section进行整个，合并成一个可执行文件（也是ELF格式）

<img src="pictures/image-20230419012532079.png" alt="image-20230419012532079" style="zoom:50%;" />

这个是可重定位文件中的.text section里面储存了main函数的汇编代码，还包含了一个relocation entry。这个relocation entry是一个数据机构，书上讲的很清楚。

<img src="pictures/image-20230419014613359.png" alt="image-20230419014613359" style="zoom: 50%;" />

这个是重定位时，会计算出运行call时PC要到的位置。这些信息就在relocation entry中。

R_X86_64_32 是一种重定位类型 绝对地址。

R_X86_64_PC32 是PC相对地址。

<img src="pictures/image-20230419014759761.png" alt="image-20230419014759761" style="zoom:50%;" />

这是再重定位之后，symbol对应了他的地址，例如sum对应了他前面的跳转地址。

<img src="pictures/image-20230419015114902.png" alt="image-20230419015114902" style="zoom:50%;" />

完成了之前的每一个symbol的地址，形成了Executable Object Files就可以将可执行文件Loading进入内存啦！

可以看到，内存中的Read/Write data segment存放.data,.bss的数据

内存中的code segment存放.text和.redata数据。

然后这个是我们熟悉的堆栈和堆，以及上面的kernel memory。程序就在这一片空间里面运行啦！！

### 库

许多函数要经常使用，我们该怎么让他们被高效地使用呢？

<img src="pictures/image-20230419110123954.png" alt="image-20230419110123954" style="zoom:50%;" />

Option1： 将所有functions放在一个文件里，但是这样每次都要将这个大文件lin，时间和空间上都不方便。

Option2： 将函数分别放在不同的file里面，由程序员选择需要的文件(relocatable files)进行link，但是对于程序员会很累。

这两个Option都不好！ 那怎么解决的？ 提出了Library的概念！

### Static Library

<img src="pictures/image-20230419111137229.png" alt="image-20230419111137229" style="zoom:50%;" />

将文件结合成成archive（是什么我也不是很懂）

<img src="pictures/image-20230419113108156.png" alt="image-20230419113108156" style="zoom:50%;" />

这些函数文件被编译成静态库（静态库可以往里面继续加文件）

<img src="pictures/image-20230419113322616.png" alt="image-20230419113322616" style="zoom:50%;" />

这是两个经常用的Library。里面打包了很多c文件函数。

**使用静态库链接：**

<img src="pictures/image-20230419113437546.png" alt="image-20230419113437546" style="zoom:50%;" />

<img src="pictures/image-20230419113456849.png" alt="image-20230419113456849" style="zoom:50%;" />

注意，因为在main()函数中调用了addvec函数.当链接器运行时，判定了mina运用了addvec符号，会将addvec.o复制到可执行文件。（没有引用的multivec文件就不会复制到可执行文件）

**静态库链接的时候，只将库里需要的文件一起链接，这样节省空间。**

<img src="pictures/image-20230419114309842.png" alt="image-20230419114309842" style="zoom:50%;" />

一般将库文件放在command line的最后面。上图中 如果按照第二行来写，将libtest.o放后面，那么会报错。因为unresolved referenfce会往后找，找不到就报错了。

<img src="pictures/image-20230419114533251.png" alt="image-20230419114533251" style="zoom:50%;" />

但是静态库也有缺点：

* 每个使用printf的文件都将printf.o文件复制并且链接，这样浪费空间。
* Load进入内存的文件都有一个printf.o的数据，运占用内存空间
* 如果修复bug需要每个文件重新链接

**所以Modern的做法是使用Shared Libraries。使用动态库可以Object files动态的链接。（在Load的时候甚至run的时候载入库文件）**

<img src="pictures/image-20230419115351816.png" alt="image-20230419115351816" style="zoom:50%;" />

动态库有两种链接的方式：

* 在Load的时候链接（Load进入内存的时候链接）
* 在run的时候链接。

<img src="pictures/image-20230419115539897.png" alt="image-20230419115539897" style="zoom:50%;" />

这个是Dynamic Linking的一种链接方式。链接产生的是Partially Linked executable object file，Load进去的才是Fully linked的可执行文件。

<img src="pictures/image-20230419115732775.png" alt="image-20230419115732775" style="zoom:50%;" />

<img src="pictures/image-20230419115855364.png" alt="image-20230419115855364" style="zoom:50%;" />

这个是在Run的时候进行链接。

dlopen打开库。dlsym将函数导入(不是很明白捏)，之后就可以运行啦。最后dlclose关掉动态库。

### 总结：

<img src="pictures/image-20230419120003169.png" alt="image-20230419120003169" style="zoom:50%;" />

这之后又讲了一个Library Interposition 但我每太学懂：《

<img src="pictures/image-20230419120044898.png" alt="image-20230419120044898" style="zoom:50%;" />

<img src="pictures/image-20230419120147315.png" alt="image-20230419120147315" style="zoom:50%;" />



<img src="pictures/image-20230419120213597.png" alt="image-20230419120213597" style="zoom:50%;" />

似乎是想对于库中的函数做一些改变，例如也可以写wrapper，对于函数做一些改变，例如打印address或者记录调用次数。

然后有三种Interpose 的方法。compile time或者 link time或者 load/run time。

我有点不太懂 就不记录了，要是要学习可以打开lecture13学习一下。

























