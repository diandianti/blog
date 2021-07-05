---
title: 多线程数据保护-互斥锁
date: 2021/7/5 21:30:00
tags:
- C
- linux
- 多线程
categories: C
excerpt: 本文是对于C语言中互斥锁的使用方式的简单介绍。
---

# 描述

互斥锁就是各个线程之间访问是互斥的，如果一个 线程申请使用了互斥锁，那么其他的线程调用互斥锁就会导致线程阻塞，直至互斥锁释放。当然也可以使用非阻塞的互斥锁调用方式。

# 详细信息

[使用互斥锁](https://docs.oracle.com/cd/E19253-01/819-7051/sync-12/index.html)

oracle 多线程指南中对于互斥锁的描述

# 相关函数

互斥锁使用的相关函数如下：

```c
#include <pthread.h>

pthread_mutex_t mutex = PTHREAD_MUTEX_INITIALIZER;
#初始化互斥锁
int pthread_mutex_init(pthread_mutex_t *restrict mutex,const pthread_mutexattr_t *restrict attr);
#销毁互斥锁
int pthread_mutex_destroy(pthread_mutex_t *mutex);

#互斥锁上锁-阻塞方式
int pthread_mutex_lock(pthread_mutex_t *mutex);
#互斥锁上锁-非阻塞方式
int pthread_mutex_trylock(pthread_mutex_t *mutex);
#互斥锁解锁
int pthread_mutex_unlock(pthread_mutex_t *mutex);
```

# 初始化方式

互斥锁有两种初始化的方式：

- 使用 pthread_mutex_init 函数初始化
- 使用 宏定义变量 PTHREAD_MUTEX_INITIALIZER 初始化

如果使用宏定义变量初始化，那么就不能调用销毁函数对于锁变量进行销毁，会出错。

# 练习代码：

**要求：**

- 创建三个线程， 分别打印 A B C字符
- 打印的ABC字符需要按照顺序 ABCABC... 的顺序打印
- 使用互斥锁保护需要读写的数据

**代码：**

```c
#include <stdio.h>
#include <stdlib.h>
#include <pthread.h>

pthread_mutex_t mutex;
int order = 0;

typedef struct __run_attr {
	char charToPrint;
	int order;
} run_attr_t;

void *thread_run(void *arg)
{
	run_attr_t *run = (run_attr_t *)arg;
	int runCount = 0;
	while (1) {
		pthread_mutex_lock(&mutex);
			if (order % 3 == run->order) {
				printf("%#lx  %c\n", pthread_self(), run->charToPrint);
				order++;
				runCount++;
			}
		pthread_mutex_unlock(&mutex);

		if (runCount == 10) {
			break;
		}
	}

	return NULL;
}

int main(int argc, char * argv[])
{
	pthread_t pidA, pidB, pidC;
	pthread_mutex_init(&mutex, 0);
	run_attr_t Aattr = {'A', 0};
	run_attr_t Battr = {'B', 1};
	run_attr_t Cattr = {'C', 2};
	pthread_create(&pidA, NULL, thread_run, &Aattr);
	pthread_create(&pidB, NULL, thread_run, &Battr);
	pthread_create(&pidC, NULL, thread_run, &Cattr);
	pthread_join(pidA, NULL);
	pthread_join(pidB, NULL);
	pthread_join(pidC, NULL);
	pthread_mutex_destroy(&mutex);
	return 0;
}
```
**Note： 在这个程序中即使不对order进行加锁，打印的数据依然是正确的，不过似乎不安全。**