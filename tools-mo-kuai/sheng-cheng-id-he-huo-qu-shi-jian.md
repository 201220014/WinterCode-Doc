---
description: 生成各类ID和获取时间的内容封装成一个info模块。
---

# 📅 生成ID和获取时间

## 当前进度

```c
.---+- include -+- tools -+- color.h
    |                     +- hint.h
    |                     +- info.h <---
    | 
    +-   src   -+- tools -+- color.c
                |         +- hint.c
                |         +- info.c <---
                |
                +- data  -+- id.txt <---
                |
                +- main.c
```

## 生成ID

在我们的项目中有三个主体需要生成唯一的ID——用户、商品、订单。

我们约定这三种ID分别以U(User), G(Good), O(Order)开头加上五位数字，从类型上讲是一个字符串。

### 接口设计

```c
// info.h

/**
 * @brief 生成一个给定类型ID
 * 
 * @param dest 储存ID的目标字符串
 * @param type ID类型，U-用户，G-商品，O-订单
 */
void genID(char* dest, char type);
```

{% hint style="danger" %}
请先思考一下自己将会怎么实现，再来看我的实现。
{% endhint %}

### 实现

```c
// info.c
#include "tools/hint.h"

#include <stdio.h>
#include <string.h>

static const char* filePath = "src/data/id.txt";

void genID(char* dest, char type) {
    int uid = 0, gid = 0, oid = 0; // user, good, order
    FILE* pf = fopen(filePath, "r");
    if (pf) {
        fscanf(pf, "%d%d%d", &uid, &gid, &oid);
        fclose(pf);
    }
    int id = 0;
    switch (type) {
        case 'U': id = uid++; break;
        case 'G': id = gid++; break;
        case 'O': id = oid++; break;
    }
    sprintf(dest, "%c%05d", type, id);
    pf = fopen(filePath, "w");
    fprintf(pf, "%d %d %d\n", uid, gid, oid);
    fclose(pf);
}
```

这里为了让程序结束后再次打开时也能够接着之前的ID生成唯一的新ID，用了一个文件`src/data/id.txt`来储存当前ID的最大值。

> 这里的ID仅仅和产生时间有关，哪怕之前有某个用户或者商品被删除了，其他的用户或者商品、订单的ID并不会因此而变化，新生成的ID也不会填补之前的ID。
>
> 因为其实商品的删除仅仅是商品的状态从销售中变成已下架，而不是将这个商品的信息彻底从系统中删除。

{% hint style="warning" %}
这里用到了文件读写的知识点，如果对这部分知识点有所模糊可以寻找相关教程复习一下。

参考教程： [https://www.runoob.com/cprogramming/c-file-io.html](https://www.runoob.com/cprogramming/c-file-io.html)

在之后的内容中，笔者会默认读者掌握了基本的C文件读写的内容。
{% endhint %}

{% hint style="warning" %}
除此之外，还用到了格式化输出到字符串`sprintf`函数，这个函数声明在头文件`string.h`中，`string.h`中的字符串处理函数会在本项目中经常用到，希望读者寻找相关教程学习一下。

参考教材： [https://www.runoob.com/cprogramming/c-standard-library-string-h.html](https://www.runoob.com/cprogramming/c-standard-library-string-h.html)

在之后的教程中，笔者会默认读者掌握了`string.h`头文件中的基本用法。
{% endhint %}

## 获取时间

在商品的信息中，我们要求提供商品的上架日期，格式比如`2022-1-14`，本质上是一个字符串，所以我们需要提供一个可以获取字符串格式的日期的函数。

### 接口设计

```c
/**
 * @brief 获取当前日期
 * 
 * @param dest 储存日期的目标字符串
 */
void getDate(char* dest);
```

{% hint style="danger" %}
请先思考一下自己将会怎么实现，再来看我的实现。
{% endhint %}

### 实现

```c
// info.c
#include "tools/hint.h"

#include <time.h>
#include <string.h>

void getDate(char* dest) {
    time_t rawtime;
    time(&rawtime);
    struct tm* timeinfo = localtime(&rawtime);
    sprintf(dest, "%d-%d-%d", timeinfo->tm_year + 1900, timeinfo->tm_mon + 1, timeinfo->tm_mday);
}
```

{% hint style="warning" %}
生成日期的函数用到了`time.h`头文件，可以先寻找相关教程学习一下。

参考教程：[https://www.runoob.com/cprogramming/c-standard-library-time-h.html](https://www.runoob.com/cprogramming/c-standard-library-time-h.html)

在之后的内容中，笔者将会默认读者了解`time.h`的基本内容。
{% endhint %}

{% hint style="success" %}
至此，我们已经基本写完了tools文件夹下需要的所有模块。

如果你阅读info.h，你应该还会发现两个宏定义，这两个宏是帮助精简打印信息函数实现所定义的宏，在之后的教程中会提及。
{% endhint %}
