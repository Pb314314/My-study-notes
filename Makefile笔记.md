# Makefile学习笔记
## 最基础的编译：
```
g++ main.cpp test.1.cpp -o main
```
## 分步骤编译：
```C++
g++ main.cpp -c//只编译不链接
g++ factorial.cpp -c
g++ printhello.cpp -c
//会生成3个.o文件
g++ *.o -o main//*.o是把所有的.o文件链接在一起
//生成了可执行文件main
```
## 使用Makefile编译
### Version1:
```
hello: main.cpp printhello.cpp factorial.cpp//目标依赖后面这些文件
    g++ -o hello main.cpp printhello.cpp factorial.cpp//使用这条命令生成目标
```

### Version2:
抽象出CXX，TARGET，OBJ文件
```
CXX = g++//指定编译器
TARGET = hello//指定目标
OBJ = main.o printhello.o factorial.o//

$(TARGET):$(OBJ)//target依赖于OBJ文件
    $(CXX) -o $(TARGET) $(OBJ)

main.o:main.cpp
    $(CXX) -c main.cpp

printhello.o: printhello.cpp
    $(CXX) -c printhello.cpp

factorial.o: factorial.cpp
    $(CXX) -c factorial.cpp
```
### Version3:
增加了CXXFLAG，不仅进行编译，还进行warning的打印。
一行指令完成了对于cpp文件的编译
```
CXX = g++//指定编译器
TARGET = hello//指定目标
OBJ = main.o printhello.o factorial.o//需要的.o文件

CXXFLAGS = -c -Wall//-c还是编译文件。-Wall是显示出所有的warning（下面CXXFLAGS会分别编译这两个命令）

$(TARGET):$(OBJ)//target依赖于OBJ文件
    $(CXX) -o $@ $ˆ//$@指的是上一行冒号之前的变量名，$ˆ 指的是上一行冒号之后的变量名

%.o: %.cpp
    $(CXX) $(CXXFLAGS) $< -o $@//这里不是很懂 但是代替了Version2中-c编译的三条

.PHONY: clean//为了防止有个文件叫做clean
clean: 
    rm -f *.o $(TARGET)//使用make clean 删除掉所有的.o文件
```

### Version4:
增加修改了SRC和OBJ两行
```
CXX = g++//指定编译器
TARGET = hello//指定目标
SRC = $(wildcard *.cpp)
OBJ = $(patsubst %.cpp, %.o, $(SRC))//把SRC中的cpp文件替换成.o文件

CXXFLAGS = -c -Wall//-c还是编译文件。-Wall是显示出所有的warning（下面CXXFLAGS会分别编译这两个命令）

$(TARGET):$(OBJ)//target依赖于OBJ文件
    $(CXX) -o $@ $ˆ//$@指的是上一行冒号之前的变量名，$ˆ 指的是上一行冒号之后的变量名

%.o: %.cpp
    $(CXX) $(CXXFLAGS) $< -o $@//这里不是很懂 但是代替了Version2中-c编译的三条

.PHONY: clean//为了防止有个文件叫做clean
clean: 
    rm -f *.o $(TARGET)//使用make clean 删除掉所有的.o文件
```
## CMake：
### 第一种CMake方法（不利于删除CMake产生的文件）
首先创建txt文件：（CMake要读的文件）
CMakeLists.txt
```
cmake_minimum_required(VERSION 3.10)//需要的CMake最低版本

project(hello)//需要可执行的项目hello

add_executable(hello main.cpp factorial.cpp printhello.cpp)//生成hello需要的文件
```
然后在shell里运行: (这样做会将文件都生成在当前文件夹)（生成Makefile）
cmake .
然后执行生成的Makefile：
make
//Makeclean不将生成的文件都删除

## 第二种 方便删除CMake生成的文件
mkdir built
cd build
cmake ..//去上一层目录中找文件
make




