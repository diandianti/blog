---
title: C语言中fork的使用
date: 2021/7/5 21:00:00
tags:
- C
- linux
categories: C
---

本文是对于C语言中fork函数的一些简单介绍。


# 简介

fork 用来创建一个新的子进程，子进程和父进程执行在fork之后的相同代码，并且子进程与父进程共享寄存器 等信息。

# 返回值

- 负值 创建失败
- 0 创建成功 表示执行fork的地方正在一个子进程里面
- 正值 创建成功 执行fork的地方在父进程里面

# 使用方式

## 简单使用

```c
#include <stdio.h>
#include <sys/types.h>
#include <unistd.h>
int main()
{
	// make two process which run same
	// program after this instruction
	fork();

	printf("Hello world?\n");
	return 0;
}
```

```
Hello world!
Hello world!
```

## fork 多次

```c
#include <stdio.h>
#include <sys/types.h>
int main()
{
	fork();
	fork();
	fork();
	printf("hello\n");
	return 0;
}
```

```c
hello
hello
hello
hello
hello
hello
hello
hello
```

**解析**

```c
fork ();   // Line 1
fork ();   // Line 2
fork ();   // Line 3

       L1       // There will be 1 child process 
    /     \     // created by line 1.
  L2      L2    // There will be 2 child processes
 /  \    /  \   //  created by line 2
L3  L3  L3  L3  // There will be 4 child processes 
                // created by line 3
```

## 问题1

如下的程序会输出什么？

```c
#include <stdio.h>
#include <sys/types.h>
#include <unistd.h>
void forkexample()
{
	// child process because return value zero
	if (fork() == 0)
		printf("Hello from Child!\n");

	// parent process because return value non-zero.
	else
		printf("Hello from Parent!\n");
}
int main()
{
	forkexample();
	return 0;
}
```

```c
1.
Hello from Child!
Hello from Parent!
     (or)
2.
Hello from Parent!
Hello from Child!
//因为子进程与父进程是在同时运行的，所以哪一个先输出是不确定的
```

**解析**

上面的代码可以写成下面的这种形式，就比较容易理解了

```c
#include <stdio.h>
#include <sys/types.h>
#include <unistd.h>
void forkexample()
{
	pid_t pid;
	
	pid = fork();
	// child process because return value zero
	if (pid == 0)
		printf("Hello from Child!\n");

	// parent process because return value non-zero.
	else
		printf("Hello from Parent!\n");
}
int main()
{
	forkexample();
	return 0;
}
```

## 问题2

如下的程序会输出什么？

```c
#include <stdio.h>
#include <sys/types.h>
#include <unistd.h>

void forkexample()
{
	int x = 1;

	if (fork() == 0)
		printf("Child has x = %d\n", ++x);
	else
		printf("Parent has x = %d\n", --x);
}
int main()
{
	forkexample();
	return 0;
}
```

```c
Parent has x = 0
Child has x = 2
     (or)
Child has x = 2
Parent has x = 0
```

**解析**

子程序和父程序在fork之前的东西是共享的，但是在fork之后的东西就都是各自的了。

# 思考题目

1. 下面的程序总共会创建多少子进程

    ```c
    for (i = 0; i < n; i++)
    	fork();
    ```

    2^n - 1

    ```c
    	F0       // There will be 1 child process created by first fork
          /     \
        F1      F1    // There will be 2 child processes created by second fork
       /  \    /  \
     F2   F2  F2   F2  // There will be 4 child processes created by third fork
    / \   / \ / \  / \
     ...............   // and so on
    ```

2. 如下的程序打印结果，假设父进程打印的值为x y ， 子进程打印的值为u v，那么这四个值之间的关系为？ u + 10 = x and v = y

    ```c
    if (fork() == 0) {
    	a = a + 5;
    	printf("%d, %p\n", a, &a);
    }
    else {
    	a = a –5;
    	printf("%d, %p\n", a, &a);
    }
    ```

    **解析：** 变量在物理内存中的地址肯定是不一致的，但是这里打印的是虚拟内存，变量的地址是一个拷贝的过程，所以是一致的。

# 参考

[fork() in C - GeeksforGeeks](https://www.geeksforgeeks.org/fork-system-call/)

[C 語言 fork 使用教學與範例，多行程 Multi-Process 平行化程式設計 - G. T. Wang](https://blog.gtwang.org/programming/c-fork-tutorial-multi-process-programming/)