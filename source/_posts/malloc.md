---
title: linux过量内存申请
date: 2021/12/21 21:00:00
update: 2021/12/21 21:00:00
tags:
- linux
- c
categories: linux
excerpt: 如何在一个只有4G物理内存的pc上面申请2T的内存空间。
---

# 起因

事情要从阅读别人的博客说起，有人在自己的博客中说可以使用malloc函数成功申请2T大小的内存，但是使用地址的时候就出错了，这引起了我的兴趣。

博文地址： [https://lemire.me/blog/2021/10/27/in-c-how-do-you-know-if-the-dynamic-allocation-succeeded/](https://lemire.me/blog/2021/10/27/in-c-how-do-you-know-if-the-dynamic-allocation-succeeded/) 

# 测试

作者说可以申请成功，还贴出了自己申请的代码，我这样好奇的人怎么可能不去自己尝试一下，代码如下：

```c
#include <stdio.h>
#include <stdlib.h>

int main() {
  size_t large = 1099511627776;
  char *buffer = (char *)malloc(large);
  if (buffer == NULL) {
    printf("error!\n");
    return EXIT_FAILURE;
  }
  printf("Memory allocated\n");
  for (size_t i = 0; i < large; i += 4096) {
    buffer[i] = 0;
  }
  free(buffer);
  return EXIT_SUCCESS;
}
```

使用gcc简单的编译一下然后运行：

```bash
❯ gcc -o mem_test mem.c
❯ ./mem_test
error!
```

输出是错误，也就是说在我自己的PC上面使用是不行的。

# 继续研究

阅读到后面发现了别人的评论，大概意思是说linux的内存的申请机制导致了这样的问题，还有人贴上了相关的文档，其中有两篇文档介绍的比较详细，链接如下

[https://www.kernel.org/doc/Documentation/vm/overcommit-accounting](https://www.kernel.org/doc/Documentation/vm/overcommit-accounting)

[https://www.win.tue.nl/~aeb/linux/lk/lk-9.html#ss9.6](https://www.win.tue.nl/~aeb/linux/lk/lk-9.html#ss9.6)

简要概括一下文档的意思：

- linux可以通过更改文件/proc/sys/vm/overcommit_memory的值更改程序过量申请内存行为
- /proc/sys/vm/overcommit_memory 文件的值为0表示可以少量的过度申请内存（只有ROOT用户才可以），明显的过量内存申请会被拒绝，并且0为默认值。
- /proc/sys/vm/overcommit_memory 文件的值为1表示同意所有的过量内存申请，任何大小的内存申请都会成功
- /proc/sys/vm/overcommit_memory 文件的值为2表示拒绝过量内存申请，可申请的内存大小为swap + 50%(默认值) * 物理内存
- /proc/sys/vm/overcommit_memory 文件的值为2时可以通过更改文件/proc/sys/vm/overcommit_ratio的值来控制可申请的最大内存

# 再次测试

明白了原理之后查看了一下 我自己的PC的状态

```bash
❯ cat /proc/sys/vm/overcommit_memory
0
```

问题找到了，是我的系统默认不可以申请那么多内存。那么就修改一下测试程序和系统配置看是否可以成功申请。

代码修改如下，使用过量申请的内存会导致系统的内存泄漏，所以我没有使用：

```c
#include <stdio.h>
#include <stdlib.h>

int main() {
  size_t large = 1024 * 1024 * 1024;
  printf("mem size %ld\n", large);
  char *buffer = (char *)malloc(large);
  if (buffer == NULL) {
    printf("error!\n");
    return EXIT_FAILURE;
  }
  printf("Memory allocated\n");
  /* for (size_t i = 0; i < large; i += 4096) {
   *   buffer[i] = 0;
   * } */
  free(buffer);
  return EXIT_SUCCESS;
}
```

编译并测试效果：

```bash
需要注意的是下面命令最前面的#不是注释符号，而是表示目前是root用户，我的PC只有在root用户下才可以修改这个文件。

# cat /proc/meminfo
MemTotal:         484252 kB
MemFree:          382248 kB
MemAvailable:     403128 kB
Buffers:           13060 kB
Cached:            22568 kB
... ...
#
# echo 1 > /proc/sys/vm/overcommit_memory
# ./mem_oom
mem size 1073741824
Memory allocated
# echo 0 > /proc/sys/vm/overcommit_memory
# ./mem_oom
mem size 1073741824
error!
```

在PC上面的表现和文档中相符，问题解决。

# 总结

如果要大量申请内存可以通过更改/proc/sys/vm/overcommit_memory文件值的方式，但是不推荐这么做，因为即使内存申请成功了，在使用的时候还是会报错，因为根本没有那么多的内存可以使用。

# END

