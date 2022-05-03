---
title: Essential C++ - Chapter 2. Procedural Programming
key: 2
author: ZE
tags: C++
---
过程化编程
<!--more-->

### **函数重载 function overloading**
当多个有不同实现的函数实例提供相似的功能时，使用重载。

重载函数的特点：
1. 参数列表必须是唯一的，互不相同的
2. 返回类型必须相同

为什么不用返回类型来重载函数？
因为在调用函数时，无法判断函数的返回类型，也就无法判断调用的是哪一个重载的函数。
用参数列表来重载函数，在调用时，编译器会将函数调用的实际参数与每个重载实例的参数进行比较，选择最佳匹配。
{:.info}

### **函数模板 function template**
变量类型参数化，适用于函数体相同，但处理的参数类型不同的情况。

例：
~~~cpp
#include <iostream>
#include <string>
#include <vector>
using namespace std;

template <typename T> 
void display_message(const string& msg, const vector<T> & vec) {
    cout << msg;
    for (int i = 0; i < vec.size(); i++) {
        T t = vec[i];
        cout << t << ' ';
    }
}
int main() {
    string msg = "Hellow\n";
    int nums[] = { 1,2,3 };
    vector<int> ivec(nums, nums + 3);
    display_message(msg, ivec);

    string strs[] = { "a","b","c" };
    vector<string> svec(strs, strs + 3);
    display_message(msg, svec);
}
~~~

模板函数也可以重载。
{:.info}

### **函数指针 pointer to function**

函数名前加“*”, 可以指向具有相同参数列表和返回值类型的函数。

例如
~~~cpp
const vector<int>* (*seq_ptr)(int);
~~~

函数指针可以直接初始化为0，表示没有指向任何函数，或者赋值为某个函数的地址。
~~~cpp
const vector<int>* (*seq_ptr)(int) = 0;

// assigns seq_ptr the address of fibon_seq()
seq_ptr = fibon_seq;
~~~

函数指针列表：
~~~cpp
const vector<int>* (*seq_array[])(int) = { 
    fibon_seq, lucas_seq, pell_seq, 
    triang_seq, square_seq, pent_seq 
};
~~~

### **头文件**

函数声明写在头文件里，函数定义写在程序文件里，便于在不同的程序里调用，但内联函数的定义应该写在头文件里。

在file scope内定义的对象（例如上文中的seq_array)，如果可能会被多个文件使用，那么应该在头文件中声明该对象。

在变量定义的前面加上关键词extern，使其成为一个声明。

~~~cpp
const int seq_cnt = 6;
extern const vector<int>* (*seq_array[seq_cnt])(int);
~~~

上述例子中的seq_cnt不需要用extern修饰的原因是：seq_cnt是一个静态变量，其定义对除该头文件以外的文件是不可见的。
{:.info}

#include “headfile.h" **vs.** #include <headfile>

简单来说，如果头文件与包括该文件在内的程序文本文件在同一目录中，我们使用引号。如果它在其他任何地方，我们使用角括号<>。
严格来说，如果文件名是由角括号包围的，则假定文件为项目或标准头文件。寻找它的搜索检查了一组预定义的位置。如果文件名是由一对引号包围的，则假定该文件是用户提供的头文件

