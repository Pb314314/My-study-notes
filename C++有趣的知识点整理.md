

# 这个文档是C++有趣的知识点整理

[toc]

**一般是一些我在刷题的时候遇到的不太理解的代码和写法用法的一些整理。希望能记录下来让自己能更加理解代码的运行!!**🐱

## 函数指针和函数对象

今天2023/3/8，好几天没有写Leetcode了，我遇到了一道**优先队列**的题目，在这个题目里面需要自定义Priority_queue的Cmp方法。这种问题我遇到很多次了，但是再次遇到还是没办法很流畅地完成。

一直不是很理解**为什么优先队列容器中的Cmp函数不能和Sort中的Cmp一样使用函数作为输入，需要使用结构体或者类的()重载**。今天想彻底理解清楚。



### 关于Sort函数的Cmp：

sort函数的使用:

sort(iterator start, iterator end,cmp)//end是尾地址的下一个地址

sort(first_pointer, last_pointer+1, cmp)

```c++
#include <iostream>
#include <algorithm>//sort在stl的这个函数中。
 
using namespace std ;
 
bool cmp(int a ,int b)
{
	return a < b ;			//从小到大排序，把 < 换成 > 就是从大到小 
}

int main()
{
	int a[10]={-1,9,5,7,-11,2,6,8,9,6};
	sort(a,a+10,cmp);//在sort中的传参，直接传入函数本身。
	for(int i = 0 ; i < 10 ; ++i)
	{
		cout<<a[i]<<" " ; 
	}
	cout<<endl ; 
	return 0 ;
}
原文链接：https://blog.csdn.net/lionel_d/article/details/41746135
```

在这段代码中的Cmp直接输入函数名。

有个疑问？？？这个输入的Cmp是什么？难道是函数指针吗？作为一个变量，除了函数指针还能是什么？



### **关于priority_queue中的cmp：**

*参考网址：*[(8条消息) c++优先队列priority_queue（自定义比较函数）_c++优先队列自定义比较_菊头蝙蝠的博客-CSDN博客](https://jutou.blog.csdn.net/article/details/122128445?spm=1001.2101.3001.6661.1&utm_medium=distribute.pc_relevant_t0.none-task-blog-2~default~CTRLIST~Rate-1-122128445-blog-53485887.pc_relevant_recovery_v2&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-2~default~CTRLIST~Rate-1-122128445-blog-53485887.pc_relevant_recovery_v2&utm_relevant_index=1)

![在这里插入图片描述](https://img-blog.csdnimg.cn/696925e85cbf4fc7b8693d3a9aa7431d.png)

这个内容解释了在容器中的cmp输入和内容。

最后一句说到：**这个cmp输入可以是函数指针或者函数对象。**

这是两个我没有很清楚的名词。函数指针在ECE391中使用过，但没有特别理解。

#### **函数指针：**

指向函数地址的变量，在CPU眼中和普通的变量没有区别。

每个函数都有一个入口地址，这个指针就指向这个地址。

函数指针的用途：

* 调用函数。
* 作为参数传入，在多线程中也有作用。

**函数指针的声明：**

```c++
//例子
int func(int x);//声明了一个函数
int (*func) (int x);//声明了一个函数指针（确定了return类型，参数个数，参数类型）
```

**函数指针调用函数：**

```c++
#include<iostream>
#include<cstdlib>
#include<vector>

using namespace std;

int AddFunc(int a, int b)  
{  
    return a + b;  
}   
int main()
{
    int (*Add1)(int a,int b);
    int (*Add2)(int a,int b);//定义了两个函数指针
 
    Add1 = &AddFunc;
    Add2 = AddFunc;//两种函数指针赋初值方法，可以加取地址符也可以不加
 
    cout << (*Add1)(3,2)<<endl; // 加*更像个指针哈哈哈
    cout<<Add1(3,2)<<endl;//输出可以加*，也可以不加
    return 0;
}
```

**函数指针传参：**

```c++
int CmpAsc(int x, int y)
{
    //如果x>y返回1，否则返回0；
    if(x>y)return 1;
    else return 0;
}
//使用函数指针传参的格式!!在后面参数的括号里面只写数据类型
void sort(int a[], int n, int (*cmp)(int,int))//最后一个参数为函数指针类型参数
{
    /*对数组a排序，排序原则由cmp指定，若cmp为真，表示两元素逆序*/
    for(int i=0;i<n-1;i++)
        for(int j=i+1;j<n;j++)
            if(cmp(a[i],a[j]))//调用传进来的函数指针
                swap(a[i],a[j]);
}

int main(int argc, const char * argv[]) {
    // insert code here...
    
    int a[6] ={8,2,1,3,4,5};
    sort(a, 6, CmpAsc);//CmpAsc函数名作为参数传递给函数sort
    for(auto &e:a)
        cout<<e<<" ";
    return 0;
}

```

```c++
#include<stdio.h>
int add(int a, int b){
    return a+b;
}
int process(int (*p)(int,int), int a,int b){
    int ret;
    ret = (*p)(a,b);
    return ret;
}
int main(){
    int num;
    num = process(add, 2,3);
    printf("num=%d\n",num);
}
```

通过函数指针，我们可以在函数中调用形式相同的不同函数，同时在多线程任务中我们可以使用使用函数指针给子线程传参。

##### 关于函数指针中typedef的使用：

我们熟悉的typedef：

```c++
int i;//定义一个整型变量i
typedef  int myInt；
myInt j;//定义一个整型变量j
//typedef的作用是给一个类型进行了重命名
```

**函数指针中typedef的使用：**

```c++
typedef char (*PTRFUN)(int); //这一行定义了函数指针类型PTRFUN 
PTRFUN pFun; //使用这个类型创建了函数指针实例
char glFun(int a){ return;} 
void main() 
{ 
    pFun = glFun; //函数指针赋值（地址）
    (*pFun)(2); //调用
}
```

这种typedef的使用使函数指针的定义方便了很多。



#### 函数对象：

函数对象的本质的操作符重载，实现了对于()操作符的重载。C++函数对象不是函数指针。但是，在程序代码中，**它的调用方式与函数指针一样，后面加个括号就可以了**。

```c++
int AddFunc(int a, int b)  
{  
    return a + b;  
}  
 
class Add//本质上定义了一个类或者结构体，在使用实例进行运算。
{
public:
    const int operator()(const int a,const int b)//重载了括号操作符就变成了函数对象。
    {
        return a+b;
    }
};
 
int main()
{
    //函数指针
    int (*Add1)(int a,int b);
    int (*Add2)(int a,int b);//声明了两个函数指针
    Add1 = &AddFunc;
    Add2 = AddFunc;//两种函数指针赋初值方法
    cout << (*Add1)(3,2)<<endl; // 5
    cout<<Add1(3,2)<<endl;//输出可以加*，也可以不加
 
    //函数对象
    Add addFunction;
    cout<<addFunction(2,3)<<endl;//调用类的重载操作符()方法
    system("pause");
    return 0;
}
```



#### 函数对象与函数指针比较

关于函数指针和函数对象的内容参考了这个链接：

[(8条消息) C++函数指针、函数对象与C++11 function对象对比分析_wuxiaoxiao2021的博客-CSDN博客](https://blog.csdn.net/vict_wang/article/details/81590984)

**函数对象可以把附加对象保存在函数对象中是它最大的优点**。

**另外，C++函数对象还有一个函数指针无法匹敌的用法：可以用来封装类成员函数指针。**

它的弱势也很明显，它虽然用起来象函数指针，但毕竟不是真正的函数指针。在使用函数指针的场合中，它就无能为力了。例如，你不能将函数对象传给qsort函数！因为它只接受函数指针。



**回到优先队列的自定义函数：**

我们来总结一下自定义cmp的方式：

**方式1：struct重载运算符()：**

```c++
struct cmp{
    //重载运算符的写法和正常函数完全一样，需要return类型和参数。
  	bool operator()(vector<int> &a, vector<int> &b){
        return a[0]>b[0];//把vector的第一个数字比较，升序排列，最小的先出来。
    }  
};
//优先队列元素：vector<int>，封装容器: vector<vector<int>>,cmp方法：结构体函数对象
priority_queue<vector<int>,vector<vector<int>>,cmp> q;//小顶堆
```

**方式2：class重载运算符()**

```c++
class cmp{
public://注意 一定要是public类哦！！！
    bool operator()(vector<int>&a,vector<int>&b){
        return a[0]>b[0]; 
    }
};
priority_queue<vector<int>,vector<vector<int>>,cmp> q;//小顶堆

```

**方式3：定义函数，使用函数指针：**

```c++
bool cmp(vector<int>&a,vector<int>&b){
	return a[0]>b[0];
}

priority_queue<vector<int>,vector<vector<int>>,decltype(&cmp)> q(cmp);//小顶堆
//其实对于这个函数指针的传入和q(cmp)不是很理解
//decltype可以获得变量的类型，类似于typeid().name()
//使用typeid(变量).name()可以获得变量的类型 可以看下面的函数。
```

![image-20230309020702391](pictures/image-20230309020702391.png)

这里其实不是很懂。所以把图片记录下来。

#### 插曲：decltype和typeid

*相关网址：*[(8条消息) 【C++深陷】之“decltype”_Jinxk8的博客-CSDN博客](https://blog.csdn.net/u014609638/article/details/106987131/)

```c++
//在学习的过程中发现了decltype和typeid的使用 在这里记录
#include<iostream>
#include <typeinfo> 
using namespace std;
 
bool cmp(int a, int b) {
    return a > b;
}
int main() {
    decltype(cmp) *t;//一个指向函数类型的指针(类型见第20行)
    decltype(&cmp) t1;//函数指针！
    bool t2;
    int a;
    t = cmp;
    t1 = cmp;//对函数指针赋值
    int value1 = t1(2,10);
    int value2 = t(10,2);
    cout<< value1 <<endl;                    //输出值：1
    cout<< value2 <<endl;                    //输出值：0
    cout << typeid(t).name() << endl;        //bool __cdecl(int,int)函数的类型形式
    cout << typeid(t1).name() << endl;       //bool (__cdecl*)(int,int)函数指针
    cout << typeid(t2).name() << endl;       //bool
    cout << typeid(a).name() << endl;       //bool
}
```

要是想获得函数指针的方法：

* 1： decltype(&func) T;
* 2:  decltype(func) *T;

#### 总结：

我觉得我应该掌握了传入函数对象的方式，也就是在结构体或类中重载()的方法。

对于函数指针的方法，我还不是很清楚为什么不能直接传入函数(类似qsort)，需要使用decltype的转换，和后面的q(cmp)。希望以后能够理解。



## static函数和引用：

*相关链接*[(8条消息) C++ sort函数中cmp()比较函数的写法_cmp函数_mmmyles的博客-CSDN博客](https://blog.csdn.net/weixin_53833977/article/details/124322406)

在这篇文章中，cmp函数中使用了静态函数，引用和const变量，有不少有趣的知识。

### 引用：

在函数传参的过程中，如果不加引用，传入的参数会拷贝出副本，修改时，原有的参数并不会被修改（因此在递归中，要修改一直修改的参数要么传递指针，要么传递引用），同时，有些变量拷贝时会消耗大量时间（例如string）。

```c++
//常见的引用：
int a =1;
int &b=a;//b是a的别名，指向同一个内存空间。

int i=5;
int *p=&i;//p是指针，指向i（&是取地址的意思）
int * &pt=p;//建立指针变量p的引用pt（&是引用的意思）
```

引用在函数传参中可以经常使用，可以起到变量一致性，不创造拷贝的作用。同时，如果不希望传入的变量被修改，可以在前面加上const进行限制。(如下面例子)（**const位置在类型之前！**）

```c++
 static bool cmp(const vector<int>& a, const vector<int>& b) {
        if (a[0] == b[0]) return a[1] < b[1];	[0]相同则比较[1]
        return a[0] > b[0];  					比较[0]
    }
```

### Static:

**Static**修饰的函数叫做静态函数，静态函数有两种，根据其出现的地方来分类：

* 出现在类里，是一个静态成员函数；

静态成员函数的作用在于：调用这个函数不会访问或者修改任何对象（非static）数据成员。

  其实很好理解:

**类的静态成员(变量和方法)属于类本身，在类加载的时候就会分配内存，可以通过类名直接去访问；**(不需要this指针，因为静态成员函数一直存在)

**非静态成员（变量和方法）属于类的对象，所以只有在类的对象产生（创建类的实例）时才会分配内存，然后通过类的对象（实例）去访问。**(有参数中有隐藏的this指针用于访问自身地址)

* 不是在类里，全局静态函数；

	#### 插曲：this指针：

	this指针不占用大小，this指针指向对象的首地址。静态成员函数先于对象存在，所有对象共享，没有this指针。

* 同时static函数只在声明的文件中可见，不能被其他文件调用。所以在其他文件中可以有名字相同的函数。

## 内存分配：

**一、存储器**

**RAM: 随机存取存储器（random access memory）**又称作“随机存储器”，是与CPU直接交换数据的内部存储器，也叫主存(内存)。它可以**随时读写**，而且**速度很快**，通常作为操作系统或其他正在运行中的程序的临时数据存储媒介。存储单元的内容可按需随意取出或存入，且存取的速度与存储单元的位置无关的存储器。这种存储器在断电时将丢失其存储内容，故主要用于存储短时间使用的程序

**ROM: 只读存储器（Read-Only Memory）**，是一种只能读出事先所存数据的固态半导体存储器。其特性是一旦储存资料就**无法再将之改变或删除**。通常用在不需经常变更资料的电子或电脑系统中，并且资料不会因为电源关闭而消失,CPU是不能直接访问的，而是需要文件系统/驱动程序(嵌入式中的EMC)将其读到RAM里面，CPU才可以访问。

看一下CPU、RAM、ROM之间的关系图

![img](https://upload-images.jianshu.io/upload_images/1853618-aafb33e343207827.png?imageMogr2/auto-orient/strip|imageView2/2/w/657/format/webp)

*作者：未来小文学家
链接：https://www.jianshu.com/p/c84facdfa422*





![img](https://upload-images.jianshu.io/upload_images/1853618-a0bbe79ab4a86345.png?imageMogr2/auto-orient/strip|imageView2/2/w/473/format/webp)

这张图片表示出在内存RAM中的不同数据分区。下图将显示数据的储存：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200315003251844.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZ2VsRGc=,size_16,color_FFFFFF,t_70)



| 存储设备名   | 所属的存储器类型 | 存储器特点                      | 功能描述               |
| ------------ | ---------------- | ------------------------------- | ---------------------- |
| 固态硬盘/U盘 | Flash            | 超大容量，可读写 非易失性存储器 | 存储数据               |
| BIOS ROM     | EEPROM           | 电擦除，只读存储器              | 存储开机启动程序       |
| 内存         | DRAM             | 大容量，可读写 易失性存储器     | 临时存储运行程序       |
| 缓存         | SRAM             | 小容量，可读写 易失性存储器     | 内存与CPU数据缓冲      |
| 南桥存储     | RAM              | 小容量，可读写                  | 存储BIOS设置如硬件接口 |

计算机启动时先运行BIOS ROM里面的程序，从CMOS RAM中读取硬件/接口设置，对硬件进行自检和初始化，然后再按BIOS设置顺序读取存储设备，最后把存储设备中的主引导记录，分区和操作系统依次调取到内存，处理器通过缓存与内存做数据交换，执行内存中的指令。

作者：张硕
链接：https://www.zhihu.com/question/53514751/answer/963487498
*相关链接：*

[计算机是如何启动的？ - 阮一峰的网络日志 (ruanyifeng.com)](http://www.ruanyifeng.com/blog/2013/02/booting.html)



## void* void指针：

**日期：**2023/3/20

*参考链接：*[(10条消息) C++ void指针（void*）简介_c++void指针_W24-的博客-CSDN博客](https://blog.csdn.net/qq_39583450/article/details/109715890)

今天偶然之间看到这个贴子，想着要么就记录一下void指针。

在介绍 void 指针前，简单说一下 void 关键字使用规则：
如果函数的参数或返回值可以是**任意类型指针**，那么应声明其类型为 void* ；（例如malloc的return void指针）
void 的字面意思是“无类型”，void*则为“无类型指针”，void不能代表一个真实的变量，void体现了一种抽象。

* 任何类型的指针都可以直接赋值给void指针，无需进行强制类型转换。
* 将void类型的指针赋值给其他类型的指针，需要进行显示转换。

```c++
double obj = 3.14, *pd = &obj;
void *pv = &obj;//可以直接将任意类型赋值给void指针
	
double *pd1 = pv;               // 错误，void指针不可以直接赋值给别的类型指针
double *pd2 = (double*)pv;      // 必须进行显示类型转换
cout << *pd2 << endl;           // 3.14
```

* void指针可以和其他类型指针比较存放的地址值是否相同：

* void指针只有强制类型转换之后才可以对其中的内容进行操作：

```c++
//我们不知道实例的类型，就无法从内存中对实例进行修改。不能操作void*指向的对象。
//从void指针的角度看，内存空间仅仅是内存空间，没办法访问内存空间中存放的对象。必须要类型强制转换之后才可以访问。
double obj = 3.14, *pd = &obj;
void *pv = &obj;//可以直接被赋值
//但如果要访问或者修改void指针指向的内容，必须要显式类型转换之后再访问。
cout << *(double*)pv << endl;   // 3.14

```

* void指针可以通过NULL，或者nullptr进行初始化，表示空指针。
* 当void指针作为函数的输入和输出时，表示可以接受任意类型的输入指针和输出任意类型的指针。（例如malloc函数的return void*，也就是可以为任意数据类型创建空间）
* 

## 函数括号后的const修饰：

*参考链接：https://blog.csdn.net/Gorgeous_mj/article/details/90574796*

**1. Const 加载函数前后的作用：**

* 加在函数前：返回值是const
* 后面加const便是函数不可以修改class的成员

**注意：**在函数后加const修饰，根本是修饰this指针，使函数不能改变类的成员变量的值。成员变量是read-only.

**又要注意！！：**函数后面的const修饰与static是不能同时出现的！！！ 也就是说函数后const和static不能同时修饰成员函数！！原因是static函数在类实例前生成，没有this函数！！当然也就不能用函数后const修饰了。

**2、const与mutable的区别**
从字面意思知道，mutalbe是“可变的，易变的”，跟constant（既C++中的const）是反义词。在C++中，mutable也是为了突破const的限制而设置的。被mutable修饰的变量（成员变量）将永远处于可变的状态，即使在一个const函数中。因此，后const成员函数中可以改变类的mutable类型的成员变量。

```c++
#include <iostream>
using namespace std;

class A{
private:
	int m_a;//int前加mutable关键字修饰即可编译通过
public:
	A():m_a(0){}
	int setA(int a) const
	{
		this->m_a = a;//error: l-value specifies const object
	}
//	mutable修饰为了突破const的限制，如果在m_a加mutable修饰，可以在const函数中修改！！
	int setA(int a)
	{
		this->m_a = a;
	}
};

int main()
{
	A a1;
	
	return 0;
}

```

**3、const成员函数与const对象**

const成员函数还有另外一项作用，即常量对象相关。对于内置的数据类型，我们可以定义它们的常量，对用户自定义的类类型也是一样，可以定义它们的常量对象。有如下规则：
①、const对象只能调用后const成员函数！！！；

②、非const对象既可以调用const成员函数，又可以调用非const成员函数。

```c++
#include <iostream>
using namespace std;

class A{
private:
	int m_a;
public:
	A():m_a(0){}
	int getA() const
	{
		return m_a;
	}
	int GetA() //非const成员函数，若在后面加上const修饰则编译通过
	{
		return m_a;
	}
	int setA(int a)
	{
		this->m_a = a;
	}
};

int main()
{
	const A a2;//const对象
	int t;
	t = a2.getA();
	t = a2.GetA();//error:const object call non-const member function,only non-const object can call


	return 0;
}

```



## 构造函数体内初始化与列表初始化的区别：

*参考链接：[(11条消息) C++构造函数体内初始化与列表初始化的区别_列表初始化和在函数体初始化的区别_西塔666的博客-CSDN博客](https://blog.csdn.net/weixin_41675170/article/details/93485655)*

大概意思是：

*  调用函数内等号赋值：调用的是赋值运算符赋值函数（等号的重载）

* 调用初始化列表语法直接调用复制构造函数。

**为了标准化，建议使用列表初始化！！注意：初始化列表的初始化顺序是按照变量的声明顺序的，并不是按照初始化列表里写的顺序！**

在类的继承中也要使用列表初始化（调用base class的构造函数）来给base class的private member赋值（衍生类不能自己给base class的private赋值）（C++ primer plus 395页）

谔谔 当然如果你不给base class的值赋值，只给衍生类赋值，编译器会自己帮你赋值。。

**！我太懒了呵呵，下次遇到再整理吧：）！**



## 关于string和char

关于string和char的我之前应该已经搞明白了，时间久了又忘记掉了。‘

关于string的索引，string的索引出来的值只能和char进行比较。

```c++
int main(){
  string s = "Hello";
	if(s[2]c == 'l'){
  	cout<< "666"<<endl;
	}
	return 1;
}
```



因为通过[]访问得到的类型是char。

同时，string和char都是的结尾都通过'\0'这个字符来作为结束的标志。（以NULL作为结尾的字符数组）

这个没有什么难度，单纯是之前忘记掉了。





## C++中的print中的\

这个是c++中正确的print方法。

\可以在引号里面打印引号。

两个\才是\。

```c++
int main(){
	cout<<"My name is:Bo Pang " <<endl;
	cout<<"This (\") is a double quote."   <<endl;
	cout<< "This (') is a single quote."  <<endl;
	cout<< "This (\\) is a backslash."    <<endl;
	cout<< "This (/) is a forward slash"  <<endl;
	return 0;
}
```



## delete 一个array需要delete[] ptr;

```c++
int* myArray = new int[5]; // Allocate an array of 5 integers on the heap

// Use the dynamically allocated array...

delete[] myArray; // Deallocate the dynamically allocated array

```

如果delet myArray，只会delete一个元素。

## Lambda 函数

这几次做leetcode经常碰到lambda函数，之前感觉这个可学可不学，只是简单了了解了语法。今天想着语法也大致懂了，不如真正学一下。

```c++
Lambda 函数主体：
[捕获列表](参数列表) -> 返回类型 {
    // Lambda函数的主体
    return 表达式;
}
```

以下是Lambda函数的主要部分：

- `[捕获列表]`：可选部分，用于指定Lambda函数需要捕获的外部变量。如果Lambda函数不需要访问外部变量，则捕获列表可以为空。捕获列表允许Lambda函数访问其所在作用域中的变量。
- `(参数列表)`：Lambda函数的参数列表，类似于普通函数的参数列表。可以为空，也可以包含一个或多个参数。
- `-> 返回类型`：可选部分，用于指定Lambda函数的返回类型。如果Lambda函数可以自动推断返回类型，则可以省略此部分。
- `{}`：Lambda函数的主体，包含实际的函数逻辑。
- `return 表达式;`：Lambda函数的返回语句，用于指定Lambda函数的返回值。

Lambda函数通常用于那些只需要在一个地方定义并使用一次的小型函数，它们的语法相对简洁。上面提供的Lambda函数示例是一个典型的Lambda函数，它不需要捕获外部变量，接受两个整数指针作为参数，并返回一个`bool`值。

Lambda函数的语法允许你以更紧凑的方式定义函数，特别是在函数作为参数传递给STL算法或函数对象时非常有用。Lambda函数可以在需要时内联定义，并且无需为其分配独立的函数名。

例子：

```c++
[](vector<int>& v1, vector<int>& v2) {
    return v1[1] < v2[1];
}

sort(courses.begin(), courses.end(),[](vector<int>& v1, vector<int>& v2){
            return v1[1]<v2[1];
        });
//这里通过lambda作为sort的Comparator传递给std::sort(比较函数需要能接受iterator(前两个参数)的传参)
```

这里的lambda没有捕获列表！ 于是我问chatgpt，能不能使用捕获列表，捕获当前函数中的参数，就不用传参数了，是可以的，只需要[&]就可以捕获定义范围内所有参数（包括栈上和堆上）。

我就有一个疑惑？难道使用[&]，需要将所有参数都放在栈上，然后调用函数？那栈不是爆炸了。

```assembly
main:
    ; 函数调用前的准备工作
    push ebp        ; 保存旧的基址指针到栈上
    mov ebp, esp    ; 设置新的基址指针
    sub esp, 8      ; 在栈上为两个整数参数分配空间

    ; 为函数参数赋值
    mov dword [ebp - 4], 10  ; 第一个参数 a = 10
    mov dword [ebp - 8], 20  ; 第二个参数 b = 20

    ; 调用 add 函数
    call add

# esp是栈顶 ebp是栈底
#将像x86中需要记录旧的指针，然后移动栈顶esp然后push参数在栈上。 那如果将所有捕获都push在stack上，很夸张。
```

有这个疑问，我向chatgpt追问。他告诉我：

**如果lambda函数没有捕获列表：**

那他就是一个正常的函数，将参数列表的参数push到栈上，进行调用。

**如果lambda函数有捕获列表：**

那这个lambda函数依赖于类存在（这个类的名称通常是一个自动生成的唯一名称，因此你无法在代码中直接引用它。Lambda函数对象在Lambda函数被定义时被创建，并且可以像函数一样被调用）。也就是一个对象函数。这就说得通了。

```text
当你定义Lambda函数并使用捕获列表时，编译器会生成一个类，该类包含了Lambda函数的实现和捕获的变量作为其成员。然后，Lambda函数的对象（实例）会在运行时创建，这个对象包括了Lambda函数实现所需的所有信息，包括捕获的变量。
```

然后lambda的函数对象重载了()，可以在函数中被调用。

```c++
#include <iostream>

int main() {
    // Lambda函数对象，重载了operator()，可以模拟不同的函数调用行为
    auto custom_function = [](int x) {
        return x * 2;
    };

    // 调用Lambda函数对象，使用不同的参数
    int result1 = custom_function(5);
    int result2 = custom_function(10);

    std::cout << "Result 1: " << result1 << std::endl;
    std::cout << "Result 2: " << result2 << std::endl;

    return 0;
}

```



这样算是明白了lambda函数的使用。

lambda没有捕获时，不依赖class存在，可以生成lambda函数对象。（也类似于类中的static，不依赖类对象存在）

lambda有捕获时，依赖class（编译器生成）存在，这个匿名class中包含了被捕获的内容，lambda函数是这个匿名类的匿名方法。

Lambda函数对象是这个匿名类的唯一实例，它可以被调用，就好像是一个普通的函数一样！

所以，Lambda函数对象实际上是指代了生成的匿名类的实例。当您调用Lambda函数对象时，实际上是在调用这个匿名类中的成员函数，这个成员函数就是Lambda函数的实现，它可以访问捕获的外部变量。

Lambda函数对象在使用时就像函数一样，但它们是匿名的，并且在编译时由编译器生成，这使得它们非常方便用于各种编程场景，特别是在需要捕获外部变量的情况下。

理解了捏。

对了 lambda后面要加;。

lambda 函数的实例对象不能作为函数指针，可以转换，但我感觉还是直接在要用函数指针的地方直接写lambda。直接写lambd结尾不用加;。

## 写完lambda顺便写一下sort函数

sort函数前两个变量用的很多，就是iterator。.begin()和.end().

最后一个参数是比较器，，std::sort()会调用这个比较器，需要能接受iterator指向的类型。

呃呃，这些我之前就知道，但是经常忘记comparator怎么写：

comparator需要return bool类型。

传入的两个参数，如果return是true就不用换位置，如果return是false就要换位置！！

所以return，就写不用交换的情况。

比如希望从小到大：

```c++
bool compare(int a, int b){
  return a<b;//前一个元素小于后一个元素 是希望的。
}
```



## lvalue和rlvalue

最近接触到C++中的move semantics,想学习一下

简单理解：

lvalue: 有储存的值;

rvalue: temporary value;

- `int&` 是一个对非常量整数的引用，因此它只能绑定到可修改的左值（lvalue）。这意味着它只能引用可以被修改的整数对象，并且不能引用临时对象或常量。
- `const int&` 是一个对常量整数的引用，因此它可以绑定到左值和右值。它可以引用任何整数对象，无论是可修改的还是常量的，还可以引用临时对象。这使得它更加通用，可以在更多的上下文中使用，包括传递给函数或绑定到表达式的结果。
- Int&&只绑定rvalue;（移动构造）move semantics

总之，`const int&` 更加灵活，可以引用更多类型的整数对象，而 `int&` 只能引用可修改的左值整数。

```c++
void PrintName1(std::string& name){//only accept lvalue
  std::cout<< name <<std::endl;
}

void PrintName2(const std::string& name){//accept lvalue和rvalue
  std::cout<< name <<std::endl;
}

void PrintName3(std::string&& name){// only accept rvalue
  std::coutM<< name <<std::endl;
}
int main(){
  std::string FirstName = "Bo";
  std::string LastName = "Pang";
  std::string Name = FirstName+LastName;
  PrintName1(Name);//可以
  PrintName1(FirstName+LastName);//不可以 don't accept rvalue
  
}
```



## 移动构造：

我终于理解了。。 移动构造中，将指针类数据类型。

**注意：只有class类型才能使用移动构造！**

对于类类型对象，移动构造函数通常会将指向堆上数据的指针从原始对象转移到新对象，同时对于非指针成员变量，会执行常规的移动或复制操作，具体取决于成员变量的类型。例如，长度信息可以通过常规的赋值操作进行复制，而不需要特殊处理。

总之，移动构造函数的目标是高效地转移资源的所有权，而不是实际的数据复制。这有助于提高性能，尤其是在处理大量数据时。

很好理解：

对于一个vector数组，他有自己的成员变量，比如数组指针，比如int来记录长度。然后数组指针指向的内容是在heap里。

（所以利用vector等传参，只要将vector的成员变量push在stack上，并不麻烦）

一般的拷贝构造会将原本vector的heap中的数据完全复制一个新的。

而移动构造会将指向堆上数据的指针从原始对象转移到新对象，同时对于非指针成员变量，会执行常规的移动或复制操作，具体取决于成员变量的类型。（也就是节省了堆上的数据的复制时间）

这意味着新的 vector 现在拥有原始 vector 中==相同的数据==，而原始 vector 中的指针会被置为空（nullptr），以==避免在其销毁时重复释放内存==。

vector的数组是动态分配的(Dynamic allocation)，也就是CS225中实现的类似hash的Dynamic Allocation。在不够的时候申请一个两倍的内存，然后将数据拷贝过去（时间开销蛮大的），使用空间占比很少的时候shrink。

move是将lvalue转化为rvalue reference,然后就可以使用

```c++
MyString str2(std::move(str1));
```

这样的移动构造。

```c++
#include <iostream>

class MyString {
public:
    // 移动构造函数没使用move
    MyString(MyString&& other) noexcept : data(other.data) {
        other.data = nullptr; // 将原对象置为空指针，避免资源被释放多次
    }

    // 构造函数
    MyString(const char* str) {
        size = strlen(str);
        data = new char[size + 1];
        strcpy(data, str);
    }

    ~MyString() {
        delete[] data;
    }

private:
    char* data;
    size_t size;
};

int main() {
    MyString str1("Hello, World!");

    // 使用移动构造函数将str1的资源移动到str2
    MyString str2(std::move(str1));

    // 此时str1不再拥有资源，data为nullptr
    std::cout << "str1: " << (str1.data == nullptr ? "nullptr" : str1.data) << std::endl;

    // str2仍然拥有资源
    std::cout << "str2: " << (str2.data == nullptr ? "nullptr" : str2.data) << std::endl;

    return 0;
}

```



```c++
#include <iostream>
#include <utility>

class MyString {
public:
    MyString(char* str) {
        size = strlen(str);
        data = new char[size + 1];
        strcpy(data, str);
    }

    // 移动构造函数
    MyString(MyString&& other) noexcept : data(other.data), size(other.size) {
        // 将其他对象置为空状态
        other.data = nullptr;
        other.size = 0;
    }

    ~MyString() {
        delete[] data;
    }

private:
    char* data;
    size_t size;
};

int main() {
    char hello[] = "Hello, World!";
    
    // 创建 MyString 对象
    MyString str1(hello);

    // 使用移动构造函数将 str1 的资源移动到 str2
    MyString str2(std::move(str1));

    // 输出结果
    std::cout << "str1: " << (str1.data == nullptr ? "nullptr" : str1.data) << std::endl;
    std::cout << "str2: " << (str2.data == nullptr ? "nullptr" : str2.data) << std::endl;

    return 0;
}

```



这里调用的是自动生成的移动构造函数:

```c++
#include <iostream>
#include <vector>

int main() {
    std::vector<int> source = {1, 2, 3, 4, 5};
    std::vector<int> destination = std::move(source); // 移动构造函数

    // 此时 source 已经不再拥有数据，可以安全地访问 destination
    for (const auto& value : destination) {
        std::cout << value << " ";
    }
    std::cout << std::endl;

    return 0;
}

```

这是自己写移动构造函数：

```c++
#include <iostream>
#include <utility>

class MyString {
public:
    // 移动构造函数
    MyString(MyString&& other) noexcept : data(std::move(other.data)) {
        // 将原对象的数据指针置为空
        other.data = nullptr;
    }

    // 构造函数
    MyString(const char* str) {
        size = strlen(str);
        data = new char[size + 1];
        strcpy(data, str);
    }

    ~MyString() {
        delete[] data;
    }

private:
    char* data;
    size_t size;
};

int main() {
    MyString str1("Hello, World!");

    // 使用移动构造函数将str1的资源移动到str2
    MyString str2(std::move(str1));

    // 此时str1不再拥有资源，data为nullptr
    std::cout << "str1: " << (str1.data == nullptr ? "nullptr" : str1.data) << std::endl;

    // str2仍然拥有资源
    std::cout << "str2: " << (str2.data == nullptr ? "nullptr" : str2.data) << std::endl;

    return 0;
}

```

关于这个move基本理解了，就是将新的实例使用旧的数据来构造，就不用拷贝了，旧的那个数据指针被设为nullptr，效率较高。

对于int等基本类型，都有默认的移动构造函数。

对于基本数据结构，没有构造函数，只能使用赋值操作符。

```c++
int a = 2;
int test = std::move(a);
//这之后a变成为定义；
```



关于lvalue和rvalue还没有完全理解。呃呃 下次再说了。





















