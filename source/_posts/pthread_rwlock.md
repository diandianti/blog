---
title: 多线程数据保护-读写锁
date: 2021/7/6 21:30:00
tags:
- C
- linux
- 多线程
categories: C
excerpt: 本文是对于C语言中pthread库的读写锁的使用方式的简单介绍。
---



# 简述

读写锁是一种共享锁，在同一时间可以有多个线程获取读锁，但是只能有一个获取写锁。即读锁之间是共享的，但是写锁之间是互斥的，并且在写的时候不能进行读操作。

# 相关函数



|   作用   |  函数    |
| ---- | ---- |
|   初始化读写锁 | [pthread_rwlock_init ](https://docs.oracle.com/cd/E19253-01/819-7051/6n919hpal/index.html#sync-51)                                                               |
| 读取读写锁中的锁 | [pthread_rwlock_rdlock](https://docs.oracle.com/cd/E19253-01/819-7051/6n919hpal/index.html#sync-65) |
| 读取非阻塞读写锁中的锁 | [pthread_rwlock_tryrdlock](https://docs.oracle.com/cd/E19253-01/819-7051/6n919hpal/index.html#sync-67)[ ](https://docs.oracle.com/cd/E19253-01/819-7051/6n919hpal/index.html#sync-67) |
| 写入读写锁中的锁 | [pthread_rwlock_wrlock](https://docs.oracle.com/cd/E19253-01/819-7051/6n919hpal/index.html#sync-71)[ ](https://docs.oracle.com/cd/E19253-01/819-7051/6n919hpal/index.html#sync-71) |
| 写入非阻塞读写锁中的锁 | [pthread_rwlock_trywrlock](https://docs.oracle.com/cd/E19253-01/819-7051/6n919hpal/index.html#sync-72) |
| 解除锁定读写锁 | [pthread_rwlock_unlock](https://docs.oracle.com/cd/E19253-01/819-7051/6n919hpal/index.html#sync-74)[ ](https://docs.oracle.com/cd/E19253-01/819-7051/6n919hpal/index.html#sync-74) |
| 销毁读写锁 | [pthread_rwlock_destroy](https://docs.oracle.com/cd/E19253-01/819-7051/6n919hpal/index.html#sync-63) |
| | |

# 注意点

如果一个进程中读锁过多，但是写锁很少，有时会导致写锁的饥饿情况，所以可以使用如下的函数来设置锁的优先级：

```c
int pthread_rwlockattr_setkind_np(pthread_rwlockattr_t *attr,
                              int pref);
int pthread_rwlockattr_getkind_np(
                              const pthread_rwlockattr_t *restrict attr,
                              int *restrict pref);
```

**参考：**

[pthread_rwlockattr_setkind_np(3) - Linux manual page](https://man7.org/linux/man-pages/man3/pthread_rwlockattr_setkind_np.3.html)

# 练习

**要求**

- 使用读写锁来保护数据
- 在主进程里面调用两次读锁
- 在一个子线程里面调用一次读锁
- 在另一个子线程里面调用写锁

```c
#include <stdio.h>
#include <stdlib.h>
#include <pthread.h>
#include <unistd.h>

pthread_rwlock_t rwlock;

void checkRes(char * funName, int ret)
{
	if (ret)
		printf("Fun %s error %d\n",
			funName, ret);
}

void *rdlockThread(void *arg)
{
	int ret = 0;

	printf("Entered thread, getting read lock\n");
	ret = pthread_rwlock_rdlock(&rwlock);
	checkRes("pthread_rwlock_rdlock", ret);
	printf("got the rwlock read lock\n");

	sleep(5);

	printf("unlock the read lock\n");
	ret = pthread_rwlock_unlock(&rwlock);
	checkRes("pthread_rwlock_unlock", ret);
	printf("thread read lock unlocked\n");

	return NULL;
}

void *wrlockThread(void *arg)
{
	int ret = 0;
	printf("Entered thread, getting write lock\n");
	ret = pthread_rwlock_wrlock(&rwlock);
	checkRes("pthread_rwlock_wrlock", ret);

	printf("Got the rwlock write lock, now unlock\n");
	ret = pthread_rwlock_unlock(&rwlock);
	checkRes("pthread_rwlock_unlock", ret);
	printf("thread write lock unlock\n");

	return NULL;
}

int main(int argc, char **argv)
{
	int ret = 0;
	pthread_t pid_read, pid_write;
	printf("Enter Testcase - %s\n", argv[0]);

	printf("Main, init the rwlock\n");
	ret = pthread_rwlock_init(&rwlock, NULL);
	checkRes("pthread_rwlock_init\n", ret);

	printf("Main grab a read lock\n");
	ret = pthread_rwlock_rdlock(&rwlock);
	checkRes("pthread_rwlock_rdlock", ret);

	printf("Main grab the same read lock again\n");
	ret = pthread_rwlock_rdlock(&rwlock);
	checkRes("pthread_rwlock_rdlock", ret);

	printf("Main create the read lock thread\n");
	ret = pthread_create(&pid_read, NULL, rdlockThread, NULL);
	checkRes("pthread_create", ret);

	printf("Mian unlock first read lock\n");
	ret = pthread_rwlock_unlock(&rwlock);
	checkRes("pthread_rwlock_unlock", ret);

	printf("Mian create the write lock thread\n");
	ret = pthread_create(&pid_write, NULL, wrlockThread, NULL);
	checkRes("pthread_create", ret);

	sleep(5);

	printf("Main unlock the second read lock\n");
	ret = pthread_rwlock_unlock(&rwlock);
	checkRes("pthread_rwlock_unlock", ret);

	printf("Main wait for the threads\n");
	ret = pthread_join(pid_read, NULL);
	ret |= pthread_join(pid_write, NULL);
	checkRes("pthread_join", ret);

	ret = pthread_rwlock_destroy(&rwlock);
	checkRes("pthread_rwlock_destory", ret);

	printf("end\n");

	return 0;

}
```

