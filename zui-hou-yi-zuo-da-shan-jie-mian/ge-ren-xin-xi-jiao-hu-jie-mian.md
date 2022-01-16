---
description: 实现个人信息交互界面
---

# 📑 个人信息交互界面

## 当前进度

```c
.---+- include -+- tools -+- color.h
    |           |         +- hint.h
    |           |         +- info.h
    |           | 
    |           +- user  -+- user.h
    |           |
    |           +- good  -+- good.h
    |           |
    |           +- order -+- order.h
    |           |
    |           +- menu  -+- menu.h
    |           |
    |           +- interface -+- interface.h <---
    |           |
    |           +- config.h
    |
    +-   src   -+- tools -+- color.c
                |         +- hint.c
                |         +- info.c 
                |
                +- data  -+- id.txt
                |         +- user.txt
                |         +- good.txt 
                |         +- order.txt
                |
                +- user  -+- user.c 
                |
                +- good  -+- good.c
                |
                +- order -+- order.c
                |
                +- menu  -+- menu.c
                |
                +- interface -+- mainInterface.c
                |            -+- adminInterface.c
                |            -+- userInterface.c
                |            -+- infoInterface.c <---
                |            -+- modifyInterface.c
                |            -+- sellerInterface.c
                |            -+- goodInterface.c
                |            -+- buyerInterface.c
                |
                +- main.c
```

## 添加辅助宏

```c
// interface.h

#define check_double\
    double m = atof(buffer);\
    while (1) {\
        if (m > 0) break;\
        illegalMessage();\
        printf("Please try again: ");\
        scanf("%s", buffer);\
        m = atof(buffer);\
    }
```

> 这个宏不止在这一个界面中用到，之后用到笔者就不提醒了。

## 个人信息交互页面的实现

```c
// infoInterface.c
#include "interface/interface.h"

#include <stdio.h>
#include <stdlib.h>

static void info() {
    userInfo(curUser);
    successMessage();
}

static void topUp() {
    char buffer[MAX_LEN];
    printf("Please input the amount of money: ");
    scanf("%s", buffer);
    check_double
    User* u = getUser(curUser);
    u->balance += m;
    successMessage();
}

static HANDLER handler[] = {info, MODIFY_Interface, topUp};
make_interface(I, NFO)
```

{% hint style="warning" %}
这里用到了stdlib.h中的atof函数，作用是字符串转双精度浮点数，这里是用它来处理用户的非法输入的，如果不清楚用法可以寻找相关教程回顾一下。

参考教程：[https://www.runoob.com/cprogramming/c-function-atof.html](https://www.runoob.com/cprogramming/c-function-atof.html)

之后笔者将默认读者知晓这个函数的用法。
{% endhint %}

{% hint style="success" %}
到此，我们完成了个人信息交互界面的实现。
{% endhint %}

