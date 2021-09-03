---
title: C语言统计代码运行时间
date: 2021/9/3 21:00:00
tags:
- C
categories: C
---

# 起因
在写代码的时候经常需要比对两个版本之间代码的速度， 每当这个时候都要重新写一遍获取时间的代码，然后格式化输出。虽然可以参考之前的代码来复制粘贴，但是这样我还是嫌麻烦，所以就有了这一段代码。

# 实现方式
我想使用最简单的方式来计时，最好是直接输入一行代码就可以，而且不能依赖额外的库,所以我使用了宏定义的形式。尝试了一下使用一行代码会比较繁琐，所以需要添加两行代码来统计。

```c
// showtime.h

#ifndef _SHOWTIME_
#define _SHOWTIME_

#include <stdio.h>
#include <time.h>
#include <stdint.h>

#define BILLION 1000000000L

#define TIME_START(__in_run_times, run_name) do {                          \
	int __run_times = __in_run_times;                                      \
	char *__run_name = run_name;                                           \
	uint64_t diff = 0;                                                     \
	struct timespec _show_time_start_, _show_time_end_;                    \
	for (int __i = 0; __i < __run_times; __i++) {                          \
		clock_gettime(CLOCK_MONOTONIC, &_show_time_start_)



#define TIME_END                                                                   \
		clock_gettime(CLOCK_MONOTONIC, &_show_time_end_);                          \
		diff += (BILLION * (_show_time_end_.tv_sec - _show_time_start_.tv_sec) +   \
			_show_time_end_.tv_nsec - _show_time_start_.tv_nsec);                  \
	}                                                                              \
	printf("\n[%s Time] Run %d times\n", __run_name, __run_times);                 \
	printf("[%s Time] Total:\t%llu ns  or  %llu us\n",                             \
			__run_name,                                                            \
			(long long unsigned int) diff,                                         \
			(long long unsigned int) diff / (1000));                               \
	printf("[%s Time] Mean: \t%llu ns  or  %llu us\n",                             \
			__run_name,                                                            \
			(long long unsigned int) diff / __run_times,                           \
			(long long unsigned int) diff / (1000 * __run_times));                 \
} while(0)

#endif //_SHOWTIME_

```

# 如何使用
使用起来就更加简单了, 代码如下，编译即可。

```c
#include "showtime.h"

int main(void)
{
    TIME_START(30, "Test");
        printf("Test show time!\n");
    TIME_END;
    return 0;
}

```
输出如下：

```bash
./test_t
Test show time!
Test show time!
Test show time!
... ...
Test show time!

[Test Time] Run 30 times
[Test Time] Total:      225318 ns  or  225 us
[Test Time] Mean:       7510 ns  or  7 us
```
