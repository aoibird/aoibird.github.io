---
layout: post
title: 线程传参
categories: Computer/OS
#  - Computer
#  - Algorithms
tags: [OS, Threads]
description:
---

创建 POSIX 线程需要使用函数 `pthread_create()`，例如：

```c
pthread_t threads[N];
for (int i = 0; i < N; i++) {
  int *datap = (int *) malloc(sizeof(int));
  *datap = i;
  pthread_create(&threads[i], NULL, print, datap);
}
```

4 个参数依次为存储线程地址、属性、执行的函数名、向线程传递的参数的指针。

上述代码问题在于内存分配了 N 次，但是没有及时释放，内存分配的指针被下一次覆盖而丢失，存在内存泄露问题。

但是如果及时释放内存，像如下代码所示：

```c
pthread_t threads[N];
for (int i = 0; i < N; i++) {
  int *datap = (int *) malloc(sizeof(int));
  *datap = i;
  pthread_create(&threads[i], NULL, print, datap);
  free(datap);
}
```

仍然有问题，因为如果线程还未开始，或线程还没有取得参数，那么释放参数存储的空间后，下一次内存分配可能会分配到同一个地址，把原来的数据更改，使得上次的线程取参数时，取得的是下一个进程的参数。比如，分配存储空间，置为整型 3，创建线程 3 ，但该线程并未开始，释放存储空间，分配内存到同一位置，整型 3 被修改为整型 4，创建线程 4，此时线程 3 取参数，取得整型 4。

自动变量也有同样的问题：

```c
pthread_t threads[N];
int data;
for (int i = 0; i < N; i++) {
  data = i;
  pthread_create(&threads[i], NULL, print, &data);
}
```

## 解决方案

使用数组存储。

```
pthread_t threads[N];
int args[N];
for (int i = 0; i < N; i++) {
  args[i] = i;
  pthread_create(&threads[i], NULL, print, &args[i]);
}
```

或者即使动态分配内存也将指针存储在一个数组里，线程结束后释放。

## 测试程序
```c
#include <stdio.h>
#include <pthread.h>
#include <stdlib.h>
#include <unistd.h>
#define N 16

void *print(void *data)
{
  int d = *(int *)data;
  printf("%d\n", d);
  pthread_exit(NULL);
}

int main(int argc, char **argv)
{
  int cas = 0;
  if (argc != 2)
    return 1;
  else if (argv[1][0] == '1')
    cas = 1;
  else if (argv[1][0] == '2')
    cas = 2;
  else ;

  pthread_t threads[N];
  int args[N];
  for (int i = 0; i < N; i++) {
    if (cas == 0) {// leak
      int *datap = (int *) malloc(sizeof(int));
      *datap = i;
      pthread_create(&threads[i], NULL, print, datap);
    }
    else if (cas == 1) {// free memory
      int *datap = (int *) malloc(sizeof(int));
      *datap = i;
      pthread_create(&threads[i], NULL, print, datap);
      free(datap);
    }
    else if (cas == 2) {// auto
      int data = i;
      pthread_create(&threads[i], NULL, print, &data);
    }
    else {// array
      args[i] = i;
      pthread_create(&threads[i], NULL, print, &args[i]);
    }
  }
  for (int i = 0; i < N; i++) {
    pthread_join(threads[i], NULL);
  }

  return 0;
}
```

## 参考
《操作系统概念》旧版矩阵乘法项目中的做法类似于第一段代码（也许是我姿势不对？），新版（9版）没有这一项目。
