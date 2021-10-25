---
title: cpack使用记录
date: 2021/10/25 21:00:00
update: 2021/10/25 21:00:00
tags:
- cmake
- linux
categories: Tool
excerpt: 使用cpack的时候遇到的问题记录。
---

# 问题：将子文件夹中的目标分别打包

在使用使用cmake的时候遇到一个问题, 我有一个工程，里面的文件大致是下面这个样子, 我想将a与b两个子文件夹中的内容在编译完成之后分别打包为a.zip 和 b.zip。但是直接在最外层的CMakeLists.txt文件中加上 "include(CPack)", 会导致a 和b文件夹中的文件都放在同一个文件夹下，还需要自己分开。

```bash
.
├── a
│   ├── a.cpp
│   ├── a.h
│   └── CMakeLists.txt
├── b
│   ├── b.cpp
│   ├── b.h
│   └── CMakeLists.txt
└── CMakeLists.txt

```

# 分析

cpack在执行的时候可以自己指定一个配置文件来规定的打包的动作，因此可以自己编写一个配置文件来控制打包的操作，但是我感觉这样比较繁琐，所以放弃这种方式。
剩下的修改方式就只能在CMakeLists.txt文件中修改打包的方式。

# 实现

在cmake的官方文档中有这样的一页介绍 https://cmake.org/cmake/help/v3.21/module/CPack.html?highlight=cpack_install_cmake_projects#variable:CPACK_INSTALL_CMAKE_PROJECTS ，大概意思是可以自己定义一个大的工程里面小工程的打包方式，所以我修改最外层的CMakeLists.txt文件内容如下, 不过这样操作有一个小问题，就是需要将打包好的整个文件解压缩之后将子文件夹分别打包。

```
cmake_minimum_required(VERSION 3.20)

project(two)

add_subdirectory(a)
add_subdirectory(b)

set(CPACK_PACKAGE_FILE_NAME two)
set(CPACK_INSTALL_CMAKE_PROJECTS
    "${CMAKE_CURRENT_BINARY_DIR}/a;a;ALL;/a"
    "${CMAKE_CURRENT_BINARY_DIR}/b;b;ALL;/b")

include(CPack)

```

# 操作方式

首先需要编译程序：

```
cmake -S . -B build && cmake --build build
```
然后打包程序：

```
cd build && cpack -G ZIP
```
这样打包出来的文件如下,只需要将two.zip 解包之后将每个文件夹分别打包即可。这样做的好处是不用修改文件install的位置而在压缩包中将文件准确的分开。 

```
two.zip
├── a
│   ├── bin
│   │   └── a
│   └── include
│       └── a.h
└── b
    ├── bin
    │   └── b
    └── include
        └── b.h
```
