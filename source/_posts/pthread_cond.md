---
title: 多线程数据保护-条件变量
date: 2021/7/5 21:30:00
tags:
- C
- linux
- 多线程
categories: C
excerpt: 本文是对于C语言中条件变量的使用方式的简单介绍。
---



# 简述

条件变量可以在线程中等待某种条件满足的时候执行后续的操作，如果条件不满足，那么程序会阻塞在调用等待条件变量的地方。

# 相关函数

```c
#include <pthread.h>
//初始化
int pthread_cond_init(pthread_cond_t *restrict cond,const pthread_condattr_t *restrict attr);
//销毁
int pthread_cond_destroy(pthread_cond_t *cond);
//等待与一定时间内等待
int pthread_cond_wait(pthread_cond_t *restrict cond, pthread_mutex_t *restrict mutex);
int pthread_cond_timedwait(pthread_cond_t *cond, pthread_mutex_t *mutex, const struct timespec *abstime);
//唤醒部分条件变量
int pthread_cond_signal(pthread_cond_t * cond);
//唤醒全部
int pthread_cond_broadcast(pthread_cond_t * cond);
```

# 详情

[oracle 多线程指南中对于条件变量的描述](https://docs.oracle.com/cd/E19253-01/819-7051/6n919hpai/index.html)

# 练习

**描述：**

- 程序有一个主进程和一个子进程，主进程执行A C操作，子进程执行B操作
- 使用条件变量来控制程序运行
- 三个不同操作运行顺序如下：

    <img src="https://i.loli.net/2021/07/05/PU9vaiwGLSEqZsh.jpg" style="zoom:67%;" />

**代码：**

```c
#include <stdlib.h>
#include <stdio.h>
#include <pthread.h>
#include <unistd.h>

int work_flag_g = 0;
pthread_mutex_t mutex_g;
pthread_cond_t cond_g;

static void * func(void *arg)
{
	printf("Time: %d sub thread work step %d\n",
		(int)time(NULL), work_flag_g);
	sleep(1);

	pthread_mutex_lock(&mutex_g);
	/*
		这里的循环可以去掉，但是如果去掉之后，如果条件在wait之前触发的话，这里就会一直等待
		形成死锁，程序无法退出，所以不建议去掉
	*/
	while (work_flag_g != 1)
		pthread_cond_wait(&cond_g, &mutex_g);
	pthread_mutex_unlock(&mutex_g);

	printf("Time: %d sub thread work step %d\n",
		(int)time(NULL), work_flag_g);
	sleep(1);//step B

	pthread_mutex_lock(&mutex_g);
	work_flag_g = 2;
	pthread_cond_signal(&cond_g);
	pthread_mutex_unlock(&mutex_g);

	printf("Time: %d sub thread work step %d\n",
		(int)time(NULL), work_flag_g);
	sleep(1);
	return NULL;
}

int main(int argc, char * argv[])
{
	printf("Time: %d sub thread work step %d\n",
		(int)time(NULL), work_flag_g);
	sleep(1);
	pthread_t pid;
	pthread_create(&pid, NULL, func, NULL);

	printf("Time: %d sub thread work step %d\n",
		(int)time(NULL), work_flag_g);
	sleep(1);//step A

	pthread_mutex_lock(&mutex_g);
	work_flag_g = 1;
	pthread_cond_signal(&cond_g);
	pthread_mutex_unlock(&mutex_g);

	pthread_mutex_lock(&mutex_g);
	while(work_flag_g != 2)
		pthread_cond_wait(&cond_g, &mutex_g);
	pthread_mutex_unlock(&mutex_g);

	printf("Time: %d sub thread work step %d\n",
		(int)time(NULL), work_flag_g);
	sleep(1);//step C

	pthread_join(pid, NULL);

	printf("Time: %d sub thread work step %d\n",
		(int)time(NULL), work_flag_g);
	sleep(1);
	return 0;

}
```
