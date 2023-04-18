---
title: Essential C++ - Chapter 3. Generic Programming
key: 5
author: ZE
tags: C++
---
STL由两部分组成：
1. 容器类 e.g. vector, list, map, set, etc.
2. 泛型算法 e.g. find(), sort(), replace(), merge(), etc.

vector和list是序列容器

map和set是关联容器

map存储的是键值对，可以通过键快速地查找到对应的值。set只存储值，用来查询是否存在某个值。

泛型算法的类型无关性由函数模板来实现，容器无关性由**迭代器**来实现。

迭代器

