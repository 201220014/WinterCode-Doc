---
description: 搭建储存和操作商品信息的底层数据结构
---

# 🪵 Good模块

## 当前进度

```c
.---+- include -+- tools -+- color.h
    |           |         +- hint.h
    |           |         +- info.h
    |           | 
    |           +- user  -+- user.h
    |           |
    |           +- good  -+- good.h <---
    |           |
    |           +- config.h <---
    |
    +-   src   -+- tools -+- color.c
                |         +- hint.c
                |         +- info.c 
                |
                +- data  -+- id.txt
                |         +- user.txt
                |         +- good.txt <---
                |
                +- user  -+- user.c 
                |
                +- good  -+- good.c <---
                |
                +- main.c
```

## 设计数据类型

根据前面所说的要求，我么可以在user.h中定义如下的用户类型：

```c
// user.h

#include "config.h"

// 用户的类型定义
typedef struct {
    char id[MAX_LEN];
    char name[MAX_LEN];
    char passwd[MAX_LEN];
    char contact[MAX_LEN];
    char address[MAX_LEN];
    double balance;
} User;
```



