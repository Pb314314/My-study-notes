## 简单记录一下CS6422的一些遇到的东西：

### Googletest 第一次接触。

```c++
#include <gtest/gtest.h>

// 要测试的函数
int Add(int a, int b) {
    return a + b;
}

// 测试用例
TEST(AddTest, PositiveNumbers) {
    EXPECT_EQ(Add(2, 3), 5);
}

TEST(AddTest, NegativeNumbers) {
    EXPECT_EQ(Add(-2, -3), -5);
}

int main(int argc, char* argv[]) {
    testing::InitGoogleTest(&argc, argv);
    return RUN_ALL_TESTS();
}
```

### 原来string可以直接find

```c++
found=str.find("needles are small",found+1,6);
  if (found!=std::string::npos)
    std::cout << "second 'needle' found at: " << found << '\n';
```

### enum 枚举类型

```c++
#include <iostream>

// 定义一个枚举类型 Season，包含四个常量值
enum Season {
    SPRING,
    SUMMER,
    FALL,
    WINTER
};

int main() {
    Season currentSeason = SUMMER;  // 初始化枚举变量

    if (currentSeason == SUMMER) {
        std::cout << "It's summer!" << std::endl;
    }

    return 0;
}
```



### c++中的声明需要;结尾，定义不需要;结尾

之前写class的时候经常忘记{}后面的; ， 也不知道为什么结尾需要；。

原因就是，变量，函数，类等在声明的时候需要结尾的分号来作为声明结尾的标记。

定义的时候不需要；。



有一个构造函数，我没写，导致了debug了很久很久。。。但不知道怎么说，反正蛮蛋疼的。

### delete 之后的pointer不会变成nullptr！

（delete之后，可以将指针设为nullptr，这样可以帮助判断是否delete来避免重复delete。）

才知道，我在做的时候一直用nullptr去判断是否成功deallocate,后来发现delete之后pointer不会变成nullptr.可以自己在delete之后设置。

```c++
Node* node = new(Node);
delete node;
#这个node pionter不会变成nullptr
```

这个叫做dangling pointer.

```text
A dangling pointer is a pointer that continues to point to a memory location after the memory it points to has been deallocated (freed). In simpler terms, it's a pointer that references memory that is no longer valid or owned by the program.
```

还有说几种会引起问题的使用：

#### **Using a Pointer After Deletion:** When you `delete` a dynamically allocated object and then attempt to access or modify that object through a pointer that still holds its address. This is a common cause of dangling pointers.

```c++
int* ptr = new int(42);
delete ptr;
int value = *ptr; // Dangling pointer
```

**Returning Local Variables:** Returning a pointer to a local variable from a function. Once the function exits, the local variable's memory is reclaimed, leaving the pointer dangling.

```
int* createAndReturnPointer() {
    int x = 42;
    return &x; // Dangling pointer
}
```

**Double Deletion:** Deleting the same memory location more than once using different pointers. This can result in undefined behavior.

```c++
int* ptr1 = new int(42);
int* ptr2 = ptr1;
delete ptr1;
delete ptr2; // Double deletion and a dangling pointer
```



### 如果构造函数中使用了new方法，就需要自己写析构函数将这个new的变量delete，不然会造成泄漏。

很多时候我们不需要写析构函数，比如我们使用的都是std中的vector这种，会调用std中的析构函数。但如果我们自己new了东西，就一定需要在析构函数中delete。



### 使用valgrind来检查内存泄漏问题。

这个工具之前没有用过，感觉很不错。

```c++
valgrind --leak-check=full ./your_program
```

他会显示内存的error，告诉你哪一行的分配导致了内存泄漏。还真是好东西。

像是数据库这种长时间运行的server，一定要避免内存泄漏问题，不然自己的运行空间会被heap一直扩大而压缩，从而导致效率下降。



### \#pragma once

这个可以避免头文件的反复include，而且比起#ifdefine更加简洁。





