---
title: C++11-智能指针
key: 7
author: ZE
tags: C++
---
<!--more-->
## 智能指针
C++原生指针在程序提前退出或者抛出异常的情况下无法释放内存，容易造成内存泄露。智能指针是C++11提供的容器，存储指向某个对象的原生指针，并在合适的时机自动释放指向的对象，防止内存泄漏。

智能指针的使用跟原生指针类似，通过解引用返回它指向的对象，通过‘->'运算符访问指向对象的成员。另外智能指针还可以通过.get()方法获取其中保存的原生指针，用swap()方法可以交换两个智能指针中保存的原生指针。
## unique_ptr
unique_ptr 顾名思义，它”独占“它所指向的对象，也就是说同一时刻只能有一个unique_ptr指向同一个对象，当unique_ptr超出作用域被销毁时，它管理的对象也被销毁。

为了保证”独占“，unique_ptr不支持拷贝和赋值（禁用拷贝构造函数和赋值操作符），但支持移动语义。

### 常见操作
```cpp
#include <memory>

int main(){
    std::unique_ptr<int> u1(new int(1));//直接初始化，不能用=初始化
    std::unique_ptr<int> u2(new int(2))

    int* tmp = u1.release(); //u1放弃指针的控制权，返回指针，u1设为空
    u2.reset(tmp); //令u2指向tmp指向的对象, 并释放u2原来指向的对象

    ////上面两行命令可以合并
    // u2.reset(u1.release());

    u2.reset(); //释放u2指向的对象
    if(u2){
        u2 = nullptr; //释放u2指向的对象，u2设为空
    }
}
```
注意，u.release()通常用来初始化另一个智能指针或者赋值给另一个智能指针，如果单独调用u.release()，u会失去它原来管理的对象的控制权，原来对象的内存也不会被释放。
{:.info}

unique_ptr并不总是管理有效的对象，被默认初始化的unique_ptr中只保存一个空指针，调用了release()或者管理的指针通过移动赋值移交给了另一个智能指针的情况下，也无法通过unique_ptr访问有效的对象，因此在使用unique_ptr之前需要判断它是否保存着有效的资源。unique_ptr可以被强制转换成一个bool类型，所以我们可以直接通过if(u)来判断它是否有效。

**std::make_unique<T>(args)**:
C++14提供了一个模板函数std::make_unique<T>(args)，该函数返回一个unique_ptr，保存用args初始化的T类型对象。最好用make_unique去初始化unique_ptr，而不是使用new构造对象。

### 自定义删除器
TBC.

## shared_ptr
shared_ptr是共享指针，允许多个共享指针指向同一个对象，并且共同维护一个引用计数，每增加一个指向该对象的共享指针，引用计数就+1，每当有一个共享指针销毁或者指向另一个对象时，引用计数-1，当引用计数为0时释放对象。

### 常见操作
.use_count()返回引用计数

.unique()如果引用计数为1返回true，否则返回false

.reset()更改共享指针指向的对象

可以用unique_ptr移动初始化shared_ptr，反之不行。
{:.info}

**std::make_shared<T>(args)**: C++11提供了模板函数make_shared<T>(args)，该函数返回一个shared_ptr，保存用args初始化的T类型对象。

引用计数是如何实现的？
shared_ptr类里保存了一个引用计数器指针，构造函数中+1，析构函数中-1
{:.info}

线程安全问题
shared_ptr引用计数用了atomic原子操作，是无锁但线程安全的，shared_ptr本身是并发读安全的，但并发写不安全。
{:.info}

## weak_ptr
weak_ptr指向一个由shared_ptr管理的对象，它没有所指向对象的控制权，它不会影响shared_ptr的引用计数。

### 常见操作
```cpp
auto p=make_shared<int>(2);
weak_ptr<int> wp(p); //用shared_ptr初始化

wp.use_count(); // 返回引用计数

if(!wp.expired()){ //若引用计数为0，返回true，否则返回false)
    wp.reset(); //置空
}

shared_ptr<int> q = wp.lock(); //若expired为true，返回一个空的shared_ptr,否则返回一个绑定在同一个对象上的shared_ptr
```

## 智能指针的误用
1. 用同一个原生指针初始化多个智能指针（包括独占指针和共享指针），会造成内存重复释放
    ```cpp
    Resource* res{ new Resource() };
    std::unique_ptr<Resource> res1{ res };
    std::unique_ptr<Resource> res2{ res };
    ```
2. 在智能指针销毁之前释放他管理的对象，也会造成内存重复释放
   ```cpp
   Resource* res{ new Resource() };
   std::unique_ptr<Resource> res1{ res };
   delete res;
   ```
3. 使用get()得到的指针初始化或reset另一个智能指针，会造成跟1类似的结果，也可能会造成shared_ptr的循环引用
   
## 参考资料
[1] https://www.learncpp.com

[2] https://www.cplusplus.com

[3] 《C++ Primer 第五版》