# C++面试相关的整理：

[toc]

### Valgrind如何检查内存：

Valgrind会通过memcheck检查每一个用户要求分配的内存空间。对于每一个内存的情况查看。



### 关于虚拟内存和page fault和cache

这一块我目前是基本搞懂了。

#### 首先，访问内存的指令的运行

当cpu运行Load指令（比如：` LD R1, [1000]`），或者Branch指令等，这种需要访问或者跳转到别的内存地址的指令时。CPU首先通过指令计算要访问的地址。⚠️注意！这里计算出来的是虚拟地址。也就是在进程空间中的地址。

#### 通过TLB，Page directory，Page table对于虚拟地址的转化

得到了虚拟地址之后，我们就需要对虚拟地址进行转化。这个内容交给MMU（Memory Management Unit）.

MMU包括这几个重要的东西：

**Page directory**: 储存在cr3中。cr3储存page directory的物理地址。每个进程都有自己的page directory，在context switch的时候需要更新cr3寄存器。

**Page table**: 通过Page directory指向Page table。 注意，page table中得到的是20bits为的physical address，还需要加上virtual address中的12位offset来得到32位的physical address。

```C
typedef struct __attribute__((packed)) table_entry {
    /* Common filed on bits 5:0 */
    uint32_t P                  :1; /* present */   
    uint32_t RW                 :1; /* read/write */
    uint32_t US                 :1; /* user/supervisor */
    uint32_t PWT                :1; /* write-through */
    uint32_t PCD                :1; /* cache disabled */
    uint32_t A                  :1; /* accessed */
    uint32_t D                  :1; // dirty
    uint32_t PAT                :1;  // PAT (Page Attribute Table)：用于进一步细化页面的缓存策略。
    uint32_t G                  :1; /* Global page */
    uint8_t  Avail              :3; /* available for system program's use */
    uint32_t Page_addr          :20;// 物理地址（再加上virtual的12位offset组成32位的offset）
} table_entry_t;
```

**TLB**: 高速缓存。在访问Page directory之前会先访问TLB来查找virtual address。如果找到了，可以很快的转化为物理地址。

并且TLB也保存了类似page table中的page信息，可以**得到20bits物理地址，和是否存在在内存中**等信息。

**TLB中可以完成虚拟地址到物理地址的转化，但是这个page仍然可能不在内存中，导致page fault！**

⚠️当TLB miss之后，会更新TLB，将新的page存在TLB中。

⚠️每一个process有自己的TLB，在context switch的时候需要flush TLB。

⚠️TLB中也包括kernel space中虚拟地址向物理地址的转换，但是由于不同process具有相同的内核空间映射，所有TLB中内核空间的映射在context switch的时候保持不变。（TLB flush只是将user space的映射关系flush）（这也是kernel space在每个process中位置相同的好处）

#### page在内存中

如果page在内存中，将通过得到的physical address(20bits + 12bitsoffset)在Cache中进行寻找。就像ECE6100中学的。Cache地址分为tag，index，offset。CPU从L1，L2逐级往下寻找。也就是Project1的内容。这个32bits定位到一个Byte的大小。往往CPU会得到那个Byte对应的一整个Block的数据。来提升空间locality。

#### page不在内存中，产生page fault

当page不在内存中，就产生了page fault。需要调用page fault handler。

如果memory满了，需要牺牲里面的page，将需要的page移进来。

然后将page从disk中移动到memory中。这个过程可能涉及到DMA（使用物理地址）。

当page加载到memory之后，操作系统将更新page table中的page信息，将虚拟地址映射到新加载的page的物理地址。

处理完page fault，访问这个需要的page。访问过程中，会将数据从memory向上缓存到L2，L1 cache。

#### 关于虚拟空间和物理空间：

对于程序，他并不知道物理空间的存在，他以为自己在直接访问物理内存。但是这中间有一层变化（MMU来完成）。

程序只能知道自己要访问的虚拟地址，通过每个Process各不相同的TLB，Page directory ，Page table来得到物理内存。（一个虚拟page可以在物理内存的任意位置。）

一个Process虚拟内存中所有有用的page都散落在物理内存中不同位置的pages（所有在虚拟空间的segment都被打碎散落，比如一个堆可能散落在不同的物理内存page上。但这不影响process对于他们的访问）。

### 一次完成IO的过程：

#### Process请求读取文件（访问内存等硬件资源）

- Process请求打开并读取一个文件。比如：应用程序调用操作系统提供的文件读取API，如在C语言中使用`fread()`函数。

#### 系统调用

- `fread()`函数内部转换为一个系统调用，通知操作系统用户需要从硬盘读取特定文件的数据。

#### 操作系统处理：

- 操作系统检查文件系统，确定文件数据在硬盘上的位置，并准备读取操作。

#### 设备驱动程序：

- 操作系统通过文件系统的设备驱动程序向硬盘发送读取命令，指定需要读取数据的硬盘扇区。

#### DMA配置

- 设备驱动程序配置DMA控制器，指定数据的源地址（硬盘上的扇区地址），目标地址（内存中的缓冲区地址），以及要传输的数据量。

#### 硬盘读取数据：

- 硬盘开始读取操作，将数据从磁盘表面的扇区读到其内部缓冲区中。

#### DMA传输数据到内存：

- 一旦数据准备好，DMA控制器将数据从硬盘的内部缓冲区直接传输到内存中的指定缓冲区，无需CPU干预。

#### 中断信号：

- 数据传输完成后，硬盘向CPU发送一个中断信号，表示I/O操作已完成。

#### 中断处理：

- CPU接收到中断信号，暂停当前任务，执行中断处理程序。该程序会处理完成的I/O操作，比如更新操作系统的内部结构，标记文件数据已经可用。

#### 通知应用程序：

- 操作系统通知应用程序文件数据已经准备好，通常是通过回调函数、事件或者状态标志。

#### 应用程序处理数据：

- 应用程序接收到通知后，处理读取到的数据，例如显示图片或文档内容给用户。

#### 用户交互

- 用户查看或编辑文件内容，应用程序可能会基于用户的进一步操作继续执行其他I/O操作。



关于PIC（外面的设备与CPU之间的桥梁。CPU只能和PIC交互，外界设备也只能和PIC交互）https://zhuanlan.zhihu.com/p/603708401

### 关于GDT和IDT（随便记记，应该不太重要）

GDT储存在GDTR中是linear address

IDT是为了处理处理器中断和exception的中断描述表。IDT是储存中断处理程序的入口地址。

比如IDT捆绑linkage，然后通过 linage进入对应的handle函数。

```c
SET_IDT_ENTRY(idt[System_Call_Vector], syscall_handler);
    idt[System_Call_Vector].reserved0 = 0;              // set the gate to interrupt gate.
    idt[System_Call_Vector].dpl = 3;                    // Allow user space to use sys call.
```



### 关于内核空间

这里介绍一下kernel space。之前对于kernel space一直没有特别写过。

#### Kernel中的部分：

系统调用的处理程序：实现各种系统调用。用户空间调用system call，然后内核处理。

设备驱动程序：控制硬件设备，让OS与硬件交互（应该指的是驱动的代码）

内核数据结构： 进程表， 文件系统缓存， 缓冲区，内核配置和状态

内存管理代码： 管理物理和虚拟内存。页表管理、内存分配和回收。

调度器： scheduler。负责调度Process。

同步原语：信号量，互斥锁，条件变量。用于进程和线程同步。

网络协议栈： 实现TCP/IP 代码，负责数据的传输和接受。

内核中也有自己的代码段，数据段，BSS段，堆，栈，等部分。但是管理会更佳严格，防止内存泄漏。

#### 内存空间映射

内核空间是每个进程的虚拟地址空间中共享的。所有进程的内存空间的最顶上，都指向相同物理内核代码和数据的内核空间区域。

内核在物理内存中的位置也是固定的（在操作系统启动时确定的），并且不会被其他的页面替换掉。（会有一个bit代表他是kernel的page）



### 关于进程的内存空间

关于Data和BSS和栈空间：

```C++
#include <stdio.h>
// 全局未初始化变量，存储在BSS段
int global_uninitialized;
// 全局初始化变量，存储在数据段
int global_initialized = 5;
void main() {
    // 局部未初始化变量，存储在栈（Stack）中
    int local_uninitialized;
    // 局部初始化变量，也存储在栈中
    int local_initialized = 10;
    // 静态未初始化变量，存储在BSS段
    static int static_uninitialized;
    // 静态初始化变量，存储在数据段
    static int static_initialized = 15;
    printf("Global uninitialized: %d\n", global_uninitialized);
    printf("Global initialized: %d\n", global_initialized);
    printf("Local uninitialized: %d\n", local_uninitialized); // 可能输出任意值，因为未初始化
    printf("Local initialized: %d\n", local_initialized);
    printf("Static uninitialized: %d\n", static_uninitialized);
    printf("Static initialized: %d\n", static_initialized);
}
```

Data字段： 初始化了的全局变量和static变量。

BSS字段：没有初始化的全局变量和static变量。（BSS字段的大小取决于有多少个没有定义的变量。在程序启动时会被自动清零，储存在BSS字段中。这些变量在内存中始终在BSS字段内。）

Stack： 储存局部变量。（局部变量是Compiler来管理）

Heap：通过操作系统管理。操作系统提供了system call： brk()和mmap()来让process请求或释放内存。

#### 

### 关于mmap：

mmap是经常遇到的一个函数，但没有记录过。

作用： 通过mmap在虚拟空间内请求一段连续区域（任意位置，不会干扰到传统内存区域，例如数据段和代码段）。调用时，一般不指定确切的虚拟内存位置，必须指定需要的空间大小。

具体：

#### 文件映射： 

可以将磁盘上的文件映射到进程的虚拟内存空间中。（进程可以像访问内存一样访问文件内容）

#### 匿名映射：

不与任何文件关联，直接在虚拟空间中分配空间。与malloc和new类似。

#### 共享内存：

可以创建多个进程之间的共享内存区域。进程间通信的一种方式。（多个进程访问同一块内存区域，无需复制数据）

多个进程的虚拟空间的一部分都映射到相同的物理内存位置。共享同一份物理内存。

#### 匿名映射的例子：

```C
#include <sys/mman.h>
#include <stdio.h>
#include <unistd.h> // For `sysconf` to get the page size
int main() {
    // Get the system page size
    long pagesize = sysconf(_SC_PAGESIZE);		//获得一个page的大小
    // Request an anonymous mapping of one page// 匿名映射，获得了内存上1个page大小的内存
    void* addr = mmap(NULL, pagesize, PROT_READ | PROT_WRITE, MAP_ANONYMOUS | MAP_PRIVATE, -1, 0);
  	// 注意，这里获得了的一个page可能映射在物理空间的任意位置。
    if (addr == MAP_FAILED) {
        perror("mmap failed");
        return 1;
    }
    // Use the allocated memory area
    // Example: Initialize the memory area to 0
    memset(addr, 0, pagesize);
    // Use the memory for something...
    // Cleanup: Unmap the memory
    if (munmap(addr, pagesize) == -1) {
        perror("munmap failed");
        return 1;
    }
    return 0;
}
```

#### 共享内存例子：

```C
#include <stdio.h>
#include <stdlib.h>
#include <sys/mman.h>
#include <fcntl.h>
#include <unistd.h>
#include <string.h>
int main() {
    // 打开一个文件用于共享内存（使用shm_open共享内存）
    int fd = shm_open("/mysharedmemory", O_CREAT | O_RDWR, 0666);
    if (fd < 0) {
        perror("shm_open");
        return 1;
    }
    // 调整映射文件的大小
    ftruncate(fd, 4096);
    // 映射内存区域
    void *addr = mmap(NULL, 4096, PROT_READ | PROT_WRITE, MAP_SHARED, fd, 0);
    if (addr == MAP_FAILED) {
        perror("mmap");
        return 1;
    }
    // 写入数据到共享内存
    char *message = "Hello, world!";
    memcpy(addr, message, strlen(message) + 1);
    // 解除映射
    munmap(addr, 4096);
    // 关闭文件描述符
    close(fd);
    return 0;
}
```

```C
#include <stdio.h>
#include <stdlib.h>
#include <sys/mman.h>
#include <fcntl.h>
#include <unistd.h>
#include <string.h>
int main() {
    // 打开共享内存
    int fd = shm_open("/mysharedmemory", O_RDONLY, 0666);
    if (fd < 0) {
        perror("shm_open");
        return 1;
    }
    // 映射内存区域
    void *addr = mmap(NULL, 4096, PROT_READ, MAP_SHARED, fd, 0);
    if (addr == MAP_FAILED) {
        perror("mmap");
        return 1;
    }
    // 从共享内存读取数据
    printf("%s\n", (char *)addr);
    // 解除映射
    munmap(addr, 4096);
    // 关闭文件描述符
    close(fd);
    return 0;
}
```



### 关于静态变量

1. **保持局部变量的状态**：在函数内部声明的`static`变量在函数调用结束后不会被销毁，而是保持其值直到下次函数调用。这对于需要保留函数间调用状态的情况非常有用。
2. **限制作用域**：在文件、类或模块的范围内使用`static`变量或函数可以限制它们的作用域，使得这些变量或函数只在定义它们的文件、类或模块内可见。这有助于封装和减少命名冲突。（C文件中的private）
3. **单一实例**：对于全局`static`变量，确保了全局变量在程序的生命周期内只有一份拷贝，即使它在多个文件中被声明为`extern`。



### 关于C++中的类和虚函数

当C++的类含有虚函数时，编译器为这个类生成一个虚函数表。这个虚函数表是一个静态数组，存储了指向类的虚函数的指针。

每个含有虚函数的类都会有自己的虚函数表，用于动态绑定（多态）。当类被继承，且派生类覆盖（override）或添加新的虚函数时，派生类也有自己的虚函数表。

#### 虚函数表的位置：

储存在程序的只读数据段，因为在编译时就确定，并且不会被修改。

#### 父类和子类的虚函数表：

如果父类有虚函数，有一个虚函数表，每个虚函数会在表中有对应entry，也就是函数入口

子类从有虚函数的父类继承，也会有自己的虚函数表。

这个子类的虚函数表包含：

对于重写的虚函数：指向子类自己实现的虚函数

对于基础未被覆盖的虚函数：指向父类的虚函数实现（子类没有）

对于新增的虚函数：指向子类的虚函数

```C++
class Base {
public:
    virtual void func() {}
};
class Derived : public Base {
public:
    void func() override {}
};
Base b;         // `b`的虚指针指向`Base`的虚函数表
Derived d;      // `d`的虚指针指向`Derived`的虚函数表
Base* bp = &d;  // 通过`bp`调用`func`时，会使用`Derived`的`func`实现
```

### 含有虚函数的类的对象

每个虚函数的类的对象在内存中都会有一个隐含的指针，指向这个类的**虚函数表**。

#### 虚函数的必要性：

多态： 虚函数使得在运行时根据对象的实际类型调用相应的函数。

动态绑定： 通过对象的虚函数表指针，在运行时解析调用哪个类的哪个虚函数。

#### 关于纯虚函数：

一个类如果包含至少一个纯虚函数，则该类成为**抽象类**。**抽象类不能被实例化**。主要为派生类提供一个接口和基础功能的框架。

纯虚函数在基类中没有实现，要求派生类必须重写这个函数。

```C++
class Base {
public:
    virtual void pureVirtualFunc() = 0; // 纯虚函数
};
```

纯虚函数支持运行时多态，允许通过基类的指针或引用来调用派生类实现的函数。（注意，即使抽象类不能实例化，但是可以声明一个基类的指针，并且调用派生类对象的函数）（我们可以使用static cast，将子类的指针或引用**向上转型**为基类的指针或者引用）

```C++
class Shape {
public:
    virtual void draw() const = 0; // 纯虚函数
};

class Circle : public Shape {
public:
    void draw() const override {std::cout << "Drawing Circle\n";}
};

class Square : public Shape {
public:
    void draw() const override {std::cout << "Drawing Square\n";}
};

void drawShape(const Shape* shape) {shape->draw();} // 多态调用

int main() {
    Circle circle;
    Square square;
    drawShape(&circle); // 输出 "Drawing Circle"
    drawShape(&square); // 输出 "Drawing Square"
}
```



### 关于重载，重写，覆盖

#### 重载：Overloading（不涉及到类的基础）

相同作用域，多个同名函数。但是他们的参数列表不同，根据调用时提供的类型和数量来选择对应的函数。

```C++
class Example {
public:
    void func(int x) {}
    void func(double x) {}
};
```

#### 重写：Override（类的基础和虚函数）

是子类中一个函数与父类的虚函数有相同的签名（名称，返回类型，参数列表完全相同）。

派生类重写基类中的虚函数类提供特定于派生类的实现。使用`override`关键字明确该函数意图重写基类的虚函数，这有助于编译器检查并确认你的重写是正确的。（并不一定要加上override才能重写）

```C++
class Base {
public:
    virtual void func() {}
};
class Derived : public Base {
public:
    void func() override {} // 明确指出这是一个重写
};
```

#### 覆盖：（类的基础，不一定是虚函数）

派生类定义了一个与基类中同名的函数时（无所谓是否为虚函数），而且签名可能相同或者不同，派生类的函数就会隐藏积累中同名的函数。

```C++
class Base {
public:
    virtual void func() {}
};
class Derived : public Base {
public:
    void func(int x) {} // 覆盖了基类中的所有func，即使参数列表不同
};

```

#### 动态绑定：

通过虚函数实现，允许在运行时根据对象的实际类型来调用相应的成员函数。它用于实现多态性。

#### 静态绑定：

发生在编译时，适用于非虚成员函数和重载函数的调用。编译器根据调用时提供的参数信息来决定调用哪个函数。

#### 虚函数析构函数的必要性：

因为如果子类有自己的在堆上的内存分配，需要释放堆上的内存，就需要实现自己的析构函数。

#### 总结：

**重载**发生在同一作用域内，函数名相同但参数列表不同。（关系到静态绑定，在编译的时候就完成了函数选择）

**重写**是派生类中的函数意图提供一个基类虚函数的特定实现。（关系到动态绑定，在运行时根据对象的实际类型决定调用哪个函数）

**覆盖（隐藏）**是派生类中的函数隐藏了基类中所有同名函数。

使用`override`关键字是为了确保你的函数是在重写基类中的虚函数，而不是由于签名错误而意外地进行了隐藏。这是C++11引入的，用于增强代码的可读性和安全性。

### 关于多态：

面向对象编程的核心概念：使得不同类的对象可以以统一的接口进行操作，而具体的行为取决于对象的类型。

#### 编译时的多态：

通过函数重载和运算符重载实现。编译器根据调用的参数类型、数量和返回类型在编译时决定使用哪个函数或运算符版本。

#### 运行时多态：

通过虚函数实现。虚函数在派生类中被覆盖，通过对象的类型决定调用的函数版本。



### 关于reinterpret_cast

这个在CS6422的Project2用到了。随便记记，应该是不常用。（强制类型转换，没有安全检查）

**在不兼容类型之间的指针转换**：如将`int*`转换为`double*`，或将类类型的指针转换为与之无关的类类型的指针。

**将指针与足够大的整数类型之间转换**：比如将指针转换为`uintptr_t`，或者将`uintptr_t`转换回指针类型。

**函数指针之间的转换**：如果函数的调用约定和参数类型完全相同，但是返回类型或者参数的const/volatile属性不同，可以使用`reinterpret_cast`来转换函数指针类型。

```C++
#include <iostream>
int main() {
    int a = 42;
    int* aPtr = &a;
    // 将int*转换为double*
    double* dPtr = reinterpret_cast<double*>(aPtr);
    // 将指针转换为uintptr_t
    uintptr_t ptrVal = reinterpret_cast<uintptr_t>(aPtr);
    std::cout << "Original int pointer: " << aPtr << std::endl;
    std::cout << "Reinterpreted double pointer: " << dPtr << std::endl;
    std::cout << "Pointer value as integer: " << ptrVal << std::endl;
    return 0;
}
```



### 关于智能指针：

智能指针也用过很多次了，CS6422 Project2和ECE6100 Lab1都用了智能指针。

通过智能指针的get()对象可以获得成员的普通指针。通过get()返回的普通指针并不会影响智能指针对象本身所管理的生命周期。

也就是说，智能指针释放资源后，通过 `get()` 返回的普通指针仍然指向已经释放的内存，可能导致**悬垂指针**的问题。因此，在使用 `get()` 获取普通指针时，应该格外小心确保不会在智能指针生命周期结束后继续使用该普通指针。

智能指针一般会储存： 指向动态分配内存的指针，引用计数，删除器（来释放资源）。

#### unique_ptr:

**unique_ptr**：unique_ptr 是 C++11 引入的一种智能指针，它独占所指向的对象。当 unique_ptr 被销毁或者通过 std::move 转移所有权时，它所管理的对象会被自动释放。unique_ptr 不能进行拷贝构造和拷贝赋值，只能通过移动语义来转移所有权。

#### shared_ptr:

shared_ptr 是 C++11 中的另一种智能指针，允许多个指针共享同一个对象。shared_ptr 使用引用计数来跟踪有多少个 shared_ptr 指向同一个对象，当最后一个 shared_ptr 被销毁时，它所管理的对象会被释放。然而，shared_ptr 会增加内存消耗，因为它需要额外的引用计数。（我当时CS6422还双向列表用了shared_ptr，这个bug还搞了我好久，我就是想不明白，为什么我都用了智能指针，还是有内存泄漏）

#### Weak_ptr:

weak_ptr 也是 C++11 引入的一种智能指针，它是为了解决 shared_ptr 的循环引用问题而设计的。weak_ptr 指向 shared_ptr 所管理的对象，但不会增加引用计数。因此，即使所有 shared_ptr 销毁了，对象也不会被释放，直到没有任何强引用（即 shared_ptr）指向它。weak_ptr 通常与 shared_ptr 结合使用，用来解决对象之间可能存在的循环引用问题。**不占用资源的所有权**



### 关于decltype

`decltype` 是 C++11 引入的一个关键字，用于获取表达式的类型。它的主要用途是在编译时获取表达式的类型，而不需要实际执行表达式。

`decltype` 通常与模板编程、泛型编程、以及自动类型推导等场景结合使用，它可以用来声明变量、函数返回类型、以及类型别名，以确保类型的正确性和一致性。

```C++
int x = 42;
decltype(x) y; // y的类型为int
```

```C++
auto cmp = [](int a, int b) {
        return a > b; // 优先队列按照从大到小的顺序排列元素
    };
    // 创建优先队列并传入比较函数
std::priority_queue<int, std::vector<int>, decltype(cmp)> pq(cmp);
```

其实还是不是很懂，这里的第三个参数是什么。

#### 优先队列构造函数的参数：

```C++
template<
    class T,
    class Container = std::vector<T>,
    class Compare = std::less<typename Container::value_type>
> class priority_queue;
```



### 锁

**互斥锁（Mutex）**：互斥锁是最常见的一种锁，它用于保护共享资源，使得同一时刻只有一个线程可以访问资源。当一个线程获得了互斥锁后，其他线程必须等待该线程释放锁才能访问共享资源。

当线程尝试获取一个已经被其他线程占用的 Mutex 时，它会被置于睡眠状态，直到 Mutex 可用。通过signal唤醒。在 C++ 标准库中，`std::condition_variable` 类提供了类似的功能，它可以用于线程之间的同步和通信，包括等待和唤醒线程。当某个条件满足时，可以调用 `notify_one()` 或 `notify_all()` 方法来唤醒等待的线程。

当资源（线程在等待时会释放CPU资源，但是如果等待的时间比较长，会造成多次线程的上下文切换）

Mutex适用于长时间的临界区保护或者等待时间较长的情况。例如文件I/O,网络通信。



**信号量（Semaphore）**：信号量是一种计数器，它可以用来控制对共享资源的访问。信号量的值表示可用资源的数量，线程可以通过执行 P（等待）和 V（释放）操作来操作信号量，以保护临界区或限制同时访问资源的数量。当n=1的时候，semaphore和mutex一样。

**递归锁（Recursive Lock）**：递归锁是一种特殊的互斥锁，允许同一个线程多次获取锁而不会造成死锁。即同一线程在拥有锁的情况下可以再次获取锁，而不会被阻塞。但是要保证每次获取的锁都要对应着释放。

**读写锁（Read-Write Lock）**：读写锁允许多个线程同时读取共享资源，但只允许一个线程写入共享资源。这种锁的设计适用于读操作频繁而写操作较少的场景，可以提高并发性能。

在 C++ 中，通常没有直接提供读取锁（read lock）和写入锁（write lock）这两种概念。相反，通常使用读写锁（Read-Write Lock）来实现对共享资源的读取和写入操作的并发性控制。

读写锁允许多个线程同时读取共享资源，但只允许一个线程写入共享资源。因此，读取锁的获取操作用于允许并发读取操作，而写入锁的获取操作用于确保写入操作的原子性。

在 C++ 标准库中，通常使用 `std::shared_mutex` 来实现读写锁。`std::shared_mutex` 提供了两种操作：

1. **读取锁（Shared Lock）**：也称为共享锁（Shared Lock），通过 `std::shared_lock` 类实现。它允许多个线程同时获取读取锁，用于允许并发的读取操作。

```C++
std::shared_mutex mutex;
std::shared_lock<std::shared_mutex> lock(mutex);
// 读取操作
```

  2.**写入锁（Exclusive Lock）**：也称为独占锁（Exclusive Lock），通过 `std::unique_lock` 类实现。它确保写入操作的原子性，同时会阻塞其他线程的读取和写入操作。

```C++
std::unique_lock<std::shared_mutex> lock(mutex);
// 写入操作
```

通过使用 `std::shared_lock` 和 `std::unique_lock`，可以实现对共享资源的读取和写入操作的并发性控制。



**自旋锁（Spin Lock）**：自旋锁是一种忙等待的锁，当线程尝试获取锁时，如果锁已被其他线程占用，它将会一直循环检查锁是否被释放。自旋锁适用于轻量级的临界区保护，但长时间的自旋会占用 CPU 资源，因此应避免在长时间的临界区内使用自旋锁。（线程在等待时会一直循环检查锁是否可用，持续占用CPU资源，但是减少上下文切换）

Spin Lock适用于短时间的临界区保护或者需要等待时间较短的情况。

**条件变量（Condition Variable）**：条件变量通常与互斥锁一起使用，用于线程间的同步。它允许一个线程等待特定的条件被满足，当条件不满足时，线程会被阻塞，并释放互斥锁；当条件被满足时，线程被唤醒并重新获得互斥锁。

条件变量在线程池中经常被使用。`std::condition_variable` 是 C++ 标准库提供的一种同步机制，用于线程间的条件变量通信。在线程池中，可以使用 `std::condition_variable` 来实现任务队列的同步和通知机制。当任务队列为空时，线程池中的线程可能会等待（阻塞）直到有新的任务添加到队列中；而当有新的任务被添加到队列中时，可以使用 `std::condition_variable` 来通知等待的线程有新任务可用。

通过结合使用 `std::condition_variable` 和 `std::mutex`，可以实现线程池中的任务队列的同步和通知机制，从而实现线程间的协调和合作。

```C++
#include <iostream>
#include <thread>
#include <queue>
#include <mutex>
#include <condition_variable>

std::queue<int> taskQueue; // 任务队列
std::mutex mutex; // 互斥量用于保护任务队列
std::condition_variable cv; // 条件变量用于线程间的通知

bool isStopped = false; // 标志位，表示线程池是否停止
void worker() {
    while (true) {
        // 加锁
        std::unique_lock<std::mutex> lock(mutex);
        // 等待任务队列非空或线程池停止的通知
      	// 如果没有任务，这个线程就等待在环境变量上。等待cv将她唤醒。
        // 阻塞线程，释放相应的互斥锁，直到满足某个条件。等待结束的条件是： task不是空的，或者stopped
        cv.wait(lock, [] { return !taskQueue.empty() || isStopped; });
        // 如果线程池停止了，退出循环
        if (isStopped && taskQueue.empty()) {
            return;
        }
        // 处理任务队列中的任务
        int task = taskQueue.front();
        taskQueue.pop();
        // 释放锁
        lock.unlock();
        // 执行任务
        std::cout << "Processing task: " << task << std::endl;
    }
}

void addTask(int task) {
    // 加锁
    std::lock_guard<std::mutex> lock(mutex);
    
    // 将任务添加到队列中
    taskQueue.push(task);
    
    // 发送通知，通知等待的线程有新任务可用
  	//选择一个在等待的线程唤醒
    cv.notify_one();
}

void stopThreadPool() {
    // 加锁
    std::lock_guard<std::mutex> lock(mutex);
    
    // 设置停止标志
    isStopped = true;
    
    // 发送通知，通知等待的线程停止
  	// 唤醒全部的等待在条件变量上的线程。
    cv.notify_all();
}

int main() {
    // 创建线程池
    std::vector<std::thread> threads;
    for (int i = 0; i < 4; ++i) {
        threads.emplace_back(worker);
    }
    
    // 添加任务到任务队列中
    for (int i = 0; i < 10; ++i) {
        addTask(i);
    }
    
    // 停止线程池
    stopThreadPool();
    
    // 等待所有线程结束
    for (auto& thread : threads) {
        thread.join();
    }
    
    return 0;
}
```



#### 锁与获得锁

锁和获得锁的对应关系：

**shared_lock**,通过共享方式获得锁，比如：std::shared_mutex。shared_lock允许多个线程同时读取共享资源。（对应共享的获得锁的方式）

**shared_mutex** 

shared_mutex允许多个线程同时读取共享资源，但只允许一个线程写入共享资源。（对应锁的性质，通过提供共享和独占两种锁的方式来控制对共享资源的访问）

当read的时候，使用shared_lock获得shared_mutex，在write的时候使用unique_lock获得shared_mutex

**shared_mutex既可以用shared_lock也可以用unique_lock获得**

```C++
#include <iostream>
#include <thread>
#include <mutex>

std::shared_mutex mutex; // 共享互斥量

void read_data() {
    std::shared_lock<std::shared_mutex> lock(mutex); // 共享锁用于读取操作
    std::cout << "Reading data..." << std::endl;
    // 读取共享资源的操作
}
void write_data() {
    std::unique_lock<std::shared_mutex> lock(mutex); // 独占锁用于写入操作
    std::cout << "Writing data..." << std::endl;
    // 写入共享资源的操作
}
int main() {
    std::thread t1(read_data);
    std::thread t2(write_data);

    t1.join();
    t2.join();

    return 0;
}

```

⚠️ ：shared_lock不用通过lock()获得或者unlock()解锁。

**unique_lock对应mutex**  独占方式的锁

unique_lock，一次只能有一个线程获得锁

mutex一次只能有一个线程持有这个锁。确保了对共享资源的原子性访问。

```C++
{
    unique_lock<mutex> my_lock(lru_mtx);
    // Move the page to the start of the linked list;
    delete_page(lru_head,current_ptr,false);
    insert_page(lru_head,current_ptr,false);
    }
```

⚠️： shared_lock和unique_lock，在其生命周期结束时会自动释放锁，也就是在作用域外自动解锁。

实现机制：

简单来说：在创建`std::shared_lock`或`std::unique_lock`对象时，会调用mutex或者shared_mutex的lock()。

在对象的生命周期结束时，会调用mutex的unlock();

**shared_lock和unique_lock是C++11标准中引入的。使得并非编程更佳方便和安全。**也可以使用mutex的lock()和unlock()来获得和释放锁。

```C++
#include <iostream>
#include <thread>
#include <mutex>
#include <shared_mutex>

std::shared_mutex mutex; // 共享互斥量
void read_data(int thread_id) {
    std::shared_lock<std::shared_mutex> lock(mutex); // 使用共享锁来读取共享资源
    std::cout << "Thread " << thread_id << " is reading data." << std::endl;
    // 读取共享资源的操作
}
int main() {
    std::thread t1(read_data, 1);
    std::thread t2(read_data, 2);

    t1.join();
    t2.join();
    return 0;
}
```



### 线程同步的机制：

线程同步是指多个线程之间协调和合作以保证数据的一致性和正确性的过程。在多线程编程中，由于多个线程可能同时访问共享资源，因此需要使用特定的同步机制来确保线程之间的正确执行顺序和数据的正确性。以下是一些常见的线程同步机制：

1. **互斥量（Mutex）**：
   - 互斥量是一种最基本的线程同步机制，用于实现对共享资源的独占式访问。一次只能有一个线程持有互斥量的锁，其他线程必须等待锁释放后才能访问共享资源。C++ 中的 `std::mutex` 就是一种互斥量的实现。
2. **条件变量（Condition Variable）**：
   - 条件变量用于在多线程之间进行信号通知和等待。一个线程可以通过条件变量等待某个条件的发生，而另一个线程则可以通过条件变量发送信号来通知等待线程条件的改变。在 C++ 中，条件变量通常与互斥量一起使用，以实现等待和通知机制，例如 `std::condition_variable` 和 `std::condition_variable_any`。
3. **原子操作（Atomic Operation）**：
   - 原子操作是一种特殊的操作，可以在不需要额外同步的情况下对共享变量进行读取和写入。原子操作保证了操作的原子性，即在多线程环境中，操作的执行是不可分割的，不会被中断。C++ 中的 `std::atomic` 提供了原子操作的支持。
4. **信号量（Semaphore）**：
   - 信号量是一种用于控制对共享资源访问的同步原语。它允许多个线程同时访问共享资源，但限制同时访问资源的线程数量。信号量可以用于解决生产者-消费者问题等同步问题。C++ 中的信号量通常需要通过第三方库来实现，例如 Boost 库中的 `boost::interprocess::interprocess_semaphore`。
5. **读写锁（Read-Write Lock）**：
   - 读写锁是一种特殊的锁机制，允许多个线程同时读取共享资源，但只允许一个线程写入共享资源。读写锁在读多写少的场景中可以提高并发性能。C++11 中引入了 `std::shared_mutex` 用于支持读写锁。

这些线程同步机制提供了不同的功能和语义，可以根据具体的需求选择合适的机制来实现线程间的同步和协作。

#### 死锁和避免死锁

Dead Lock: 你等我，我等你。两个或多个线程在争夺资源时，造成的相互等待的僵局。

避免死锁：

 保持锁的顺序一致；避免嵌套锁（避免一个线程持有锁的同时请求另一个锁）；锁超时（不能在给定时间获得锁，放弃操作）；一定时间获取不了锁，kill itself





### 并发与并行

如果在单处理器上，并发程序的开销比该程序的所有部分都顺序执行的开销大，因为其中增加了上下文切换的代价（创建线程（Thread Control Block），线程调度）。表面看，将程序的所有部分当作单个的任务运行好像是开销更小点，还可以节省上下文切换的代价。

这个问题变得有些不同的是**阻塞**。如果程序中的某个任务因为该程序控制范围之外的某些条件（通常是I/O）而导致不能继续执行，那么我们就说这个任务或线程阻塞了。如果没有并发，则整个程序都将停止下来，直至外部条件发生变化。但是，如果使用并发来编写程序，那么当一个任务阻塞时，程序中的其他任务还可以继续执行，因此这个程序可以保持继续向前执行。事实上，从性能的角度看，如果没有任务会阻塞，那么在单处理机器上使用并发就没有任何意义。” ——《Java编程思想》

创建线程： 分配资源（Thread Control Block, TCB）。初始化线程，寄存器值。设置程序计数器PC，调度线程。

**在Python中由于有GIL锁，多个线程是并发而不是并行。在C++中可以将多线程在多核CPU上运行，实现真正的并发！**

Openmp， MPI， cuda都可以利用多个cpu来实现真正的并发。



### 关于Makefile

突然闲的蛋疼，问了问gpt makefile到底是个啥。也随便记记。

makefile make的时候编译了cpp文件，生成了可执行文件ELF。 会执行编译和链接两个主要操作。

**编译**： 将cpp文件转换成目标文件(.o)。对代码进行词法分析、语法分析、语义分析和代码生成。生成的目标文件包含机器代码和符号表。生成了重定向文件。

**链接**：将多个目标文件和库文件链接在一起，生成可执行文件。链接器会解析目标文件之间的依赖关系，将符号解析并合并，生成可执行文件。

在执行Makefile的时候，实际上是在运行一个名叫Make的工具。（一个系统中的文件，类似ls。用C语言编写）

Make 是一个用于自动化构建程序的工具，它读取 Makefile 中的规则，并根据这些规则来执行相应的操作，例如编译源文件、链接目标文件、生成可执行文件等。

在编译过程中，Make 工具会调用编译器来编译源文件，然后调用链接器来链接目标文件。编译器和链接器是如何加入进来的，取决于 Makefile 中的配置和规则。通常情况下，Makefile 中会定义一些变量来指定编译器和链接器的路径和参数，然后在规则中使用这些变量来调用相应的工具。

Makefile 使用一种特定的语法来定义规则，其中包括了目标、依赖和命令。Make 工具会读取 Makefile 文件，并根据其中的规则来执行相应的操作，以完成程序的构建过程。



### 空类和空类的函数

空类这个概念我之前都没听说过。。。

空类就是里面没有存真的数据的类。

```C++
#include<iostream>
using namespace std; 
class test
{
};

int main()
{
	test a, b;
	cout << "sizeof(test): " << sizeof(test) << endl;		//空类会占一个字节。确保每个实例有一个独一无二的地址。
	cout << "addr of a: " << &a << endl;
	cout << "addr of b: " << &b << endl;
	system("pause");
	return 0;
}
```

#### 空类里有哪些函数：

- 构造函数：用于创建对象。
- 析构函数：用于销毁对象并释放资源。
- 拷贝构造函数：用于创建一个新对象作为另一个现有对象的副本。
- 赋值运算符：用于复制对象。
- 地址运算符：返回对象的地址。



### struct和class

**默认的访问权限**：

- 在`class`中，默认的成员访问权限是`private`。这意味着，除非你明确指定，否则类的成员（数据和函数）在类外部是不可访问的。
- 在`struct`中，默认的成员访问权限是`public`。这意味着，除非你明确指定，否则结构体的成员在类外部是可访问的。

**默认的继承权限**：

- 当使用`class`继承时，默认继承类型是`private`。这意味着基类的公有成员和保护成员在派生类中将成为私有成员和保护成员。
- 当使用`struct`继承时，默认继承类型是`public`。这意味着基类的公有成员和保护成员在派生类中的访问权限不变。



### struct内存对齐

比如一个32位CPU，一次可以读取或者写入4字节数据。为了提高访问速度，采用内存对齐。

```C++
struct MyStruct {
    char a;    // 占用1字节
    int b;     // 通常占用4字节
    char c;    // 占用1字节
};

+---+---+---+---+---+---+---+---+---+---+---+---+
| a |padding|       b       | c |    padding    |
+---+---+---+---+---+---+---+---+---+---+---+---+
  0   1 2 3      4 5 6 7      8       9 10 11
```

⚠️：关于32位cpu。32指的是CPU的总线宽度，意味着CPU一次可以处理、传输32位的数据。

也意味着CPU使用32位的地址来定位内存，这也决定了指针的大小（最大）为32位，也对应着最大的地址空间是4GB。所以32位系统和处理器每个进程有（最大）4GB的虚拟内存地址空间。（当然32位的CPU可以只设计24位的地址总线，不一定要32位）

### Volatile

一个关键字，告诉编译器，一个变量的值可能以程序未明确指定的方式改变。使用volatile关键字声明的变量不应该被编译器优化。（每次从对应的地址读取，而不是从缓存中获取可能过时的值）

#### 主要用途

**硬件访问**：访问映射到硬件设备的内存位置时，这些内存位置的值可能随时改变（如读取传感器数据）。使用`volatile`可以确保每次访问都直接从其对应的硬件地址读取，而不是从缓存中获取可能过时的值。

**中断服务程序（ISR）**：在中断服务程序中使用的全局变量应该声明为`volatile`，如果这些变量同时被ISR和主程序访问。这确保了主程序总是获取最新的值，而不是编译器为了优化而缓存的旧值。

**多线程应用**：在多线程环境中，一个线程可能修改另一个线程可以访问的变量。声明这些共享变量为`volatile`可以防止编译器做出不安全的优化决策。

#### 注意：

- `volatile`并不意味着“原子性”。对`volatile`变量的操作（如增加操作）可能不是原子的，即它们可能在执行中被其他线程或中断打断。
- 使用`volatile`可能会降低程序的性能，因为它禁止了编译器进行某些优化。
- 在多线程编程中，仅依靠`volatile`来实现线程安全是不够的。通常需要使用锁（如互斥锁）或其他同步机制来保证操作的原子性和内存可见性。

### 野指针（Dangling pointer）

1. **已经释放的内存**：当指针所指向的内存被释放（如通过`free`或`delete`操作）后，该指针未置为`NULL`（或其他值），仍然保留原来的地址。这时，指针就成为了野指针。
2. **局部变量的地址返回**：函数内部声明的局部变量在函数返回后会被销毁。如果返回这些局部变量的地址，调用者接收到的将是一个野指针。
3. **未初始化的指针**：声明指针变量但未显式初始化，它可能指向任何内存地址。使用这样的指针很可能导致未定义行为。

#### 如何避免野指针

1. **初始化指针**：声明指针时，立即初始化为`NULL`或有效的内存地址，以防止未初始化的指针导致问题。
2. **释放后置空**：释放指针指向的内存后，立即将指针置为`NULL`。这样即使之后错误地使用了这个指针，也不太可能导致严重问题，因为尝试解引用`NULL`指针通常会导致立即崩溃，这比潜在的不稳定行为更容易检测和修复。
3. **小心使用局部变量的地址**：避免从函数返回局部变量的地址。如果需要返回复杂数据，考虑使用动态分配的内存或其他方法。
4. **使用智能指针（C++）**：在C++中，可以使用智能指针（如`std::unique_ptr`、`std::shared_ptr`）自动管理内存。这些智能指针可以帮助避免内存泄露和野指针问题，因为它们会自动释放所管理的内存，并且在适当的时候将指针置空。



### static关键字

1. **局部变量**：当`static`用于函数内的局部变量时，它会改变该变量的存储期从自动存储期（automatic storage duration，即局部变量通常的存储期）变为静态存储期（static storage duration）。这意味着变量在程序执行期间只初始化一次，并且在函数调用之间保持其值，直到程序结束。
2. **全局变量和函数**：当`static`用于文件作用域的变量或函数时，它会改变这些变量或函数的链接性从外部链接（external linkage）变为内部链接（internal linkage）。这意味着这些变量或函数只能在定义它们的文件内部访问，对其他文件不可见，从而避免了命名冲突。

避免全局变量冲突的一种方式是使用`static`使得变量或函数仅在其定义的文件内可见，或者采用独特的命名约定。

### extern 关键字

1. **全局变量**：`extern`关键字用于声明一个变量或函数，在其他地方（通常是另一个文件中）已经定义。对于全局变量，`extern`声明表明该变量的定义存在于程序的其他部分，从而允许在不同的文件中访问同一个全局变量。

2. **避免冲突**：`extern`本身并不是为了避免全局变量冲突而设计的，它的目的是为了提供一种方式来在不同的文件之间共享变量或函数。



最近在学kuiperinfer，看代码看到了关于单例模式，涉及到static在这之中的运用，记录一下。原来static这么复杂......

### Static变量

#### Static Variables Inside Functions

Static variables declared within functions maintain their value between function calls but are only accessible within that function. Each static variable inside a function is unique to that function’s scope:

- **Persistence**: They are initialized only once and retain their state between function calls.
- **Visibility**: They are only visible within the function, not outside of it.

**Example**:

```c++
cppCopy code
void count() {
    static int counter = 0;  // Only initialized once
    counter++;
    std::cout << "Counter: " << counter << std::endl;
}

int main() {
    count();  // Prints "Counter: 1"
    count();  // Prints "Counter: 2"
    // 'counter' is not accessible here, only within 'count'
}
```

#### Static Variables at Global Scope or in Classes

When declared outside of all functions (global scope) or as static members of classes, static variables have broader visibility and a single instance across the program or all instances of the class:

所以是不是可以这样理解：

全局的static变量是全局唯一的（没有static的全局变量也是全局唯一的）

（区别是static全局变量只在declare的文件可见。global variable可以被any别的文件访问，使用 external）。

```c++
// File: main.cpp
int globalVar = 10; // Accessible from other files if declared with extern

// File: another.cpp
extern int globalVar;

void printGlobalVar() {
    std::cout << "Global Var: " << globalVar << std::endl;
}
```



类成员中的static变量：在这个类的所有实例中都共用一个。因为static变量只会initialize一次，并且是线程安全的。

- **Global Static Variables**: They are initialized once and accessible from any part of the program that includes their declaration, making them unique across the entire program.
- **Static Class Members**: These are shared across all instances of a class, meaning there is only one copy for the entire class, regardless of how many instances are created.

**Example**:

```c++
cppCopy code
static int globalCounter = 0;  // Global static variable, accessible globally where declared

class Example {
    static int count;  // Static member variable, one per class, not per instance
};

int Example::count = 0;

int main() {
    globalCounter++;
    Example::count++;
}
```

#### Uniqueness

- **Function Scope Statics**: Unique within the function they are declared in.
- **Global Scope Statics and Static Class Members**: Unique in the context of the entire program or class. They provide a single instance accessible from various parts of the program or any instance of the class.

In summary, whether static variables are considered "globally unique" depends on their scope of declaration. Static variables within functions are unique to those functions. Those at the global scope or as part of classes are unique in a broader sense, applicable across the entire program or all instances of a class, respectively.

所以static在类中，可以实现线程安全的单例模式。

类中的static变量只在这个变量第一次使用的时候initialize。

### static的initialization和储存位置

global static variables are initialized only once. The initialization occurs before the program starts executing the main function and after all dynamic memory allocations are made. This initialization guarantees that the variable is ready to use by the time the program begins execution, and it persists for the lifetime of the application.

Global static variables, local static variables within functions, and static class members all share a common trait: they are initialized only once and maintain their values for the lifetime of the program. Here’s a brief overview:

1. **Storage**: All types of static variables are stored in the Data Segment of the program's memory. This includes both initialized (.data section) and uninitialized (.bss section) static variables.
2. **Initialization**:
   - **Global Static Variables and Static Class Members** are initialized before the program’s `main()` function begins execution. This ensures they are ready to use throughout the program.
   - **Local Static Variables** are initialized the first time their function is called, and their initialization only occurs once.
3. **Visibility**:
   - **Global Static Variables** are accessible throughout the file in which they are declared but are confined to their translation unit unless marked with `extern`.
   - **Local Static Variables** are only visible within the function that declares them, yet they retain their values between calls to that function.
   - **Static Class Members** are accessible from any instance of the class or directly via the class itself, depending on their access specifiers.

Each type of static variable plays a critical role in managing state and behavior in software applications, providing persistent storage and controlled access across different parts of a program.



这个帖子讲了关于全局变量，全局静态变量，类静态成员变量的初始化。

https://www.cnblogs.com/dylan-liang/p/14407237.html



局部静态变量空间分配在程序运行之前。但是初始化在第一次使用的时候。

1. **Allocation**: The memory for a local static variable is allocated when the program starts, similar to global variables. This is part of the program's static storage allocation. Even though the memory is allocated, the variable isn't constructed yet.
2. **Initialization**: The initialization of a local static variable occurs the first time control passes through its declaration. This is known as "lazy initialization" because the variable is not initialized until it's needed.

例子：

```C++
void myFunction() {
    static int myStaticVar = computeValue(); // computeValue() is called only the first time myFunction() is called
}
int main() {
    myFunction(); // myStaticVar is initialized here
    myFunction(); // myStaticVar is not re-initialized; it retains its value
    return 0;
}
```


Yes, that's correct—local static variables have a unique behavior:

1. **Allocation**: The memory for a local static variable is allocated when the program starts, similar to global variables. This is part of the program's static storage allocation. Even though the memory is allocated, the variable isn't constructed yet.
2. **Initialization**: The initialization of a local static variable occurs the first time control passes through its declaration. This is known as "lazy initialization" because the variable is not initialized until it's needed.

Here's an example to illustrate this:

```
cppCopy code
void myFunction() {
    static int myStaticVar = computeValue(); // computeValue() is called only the first time myFunction() is called
}

int main() {
    myFunction(); // myStaticVar is initialized here
    myFunction(); // myStaticVar is not re-initialized; it retains its value
    return 0;
}
```

In the example above, `myStaticVar` is only initialized during the first call to `myFunction()`. Subsequent calls to `myFunction()` do not re-initialize `myStaticVar`; it retains its value between calls. Even though `myFunction()` could be called multiple times, `computeValue()` will only be invoked once to initialize `myStaticVar`.

The first-time initialization ensures that:

- The variable is initialized in a thread-safe way (since C++11).
- The variable is only initialized if the function is actually called, avoiding unnecessary work if the function is never used.



