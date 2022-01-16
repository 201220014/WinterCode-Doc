---
description: 修改个人信息交互界面的实现
---

# ✒ 修改个人信息交互界面

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
                |            -+- infoInterface.c
                |            -+- modifyInterface.c <---
                |            -+- sellerInterface.c
                |            -+- goodInterface.c
                |            -+- buyerInterface.c
                |
                +- main.c
```

## 添加辅助宏

```c
// interface.h

#define make_modify(TYPE, W, HO) \
static void modify_##TYPE() {\
    char buffer[MAX_LEN];\
    printf("Please input new %s: ", #TYPE);\
    scanf("%s", buffer);\
    W##HO* x = get##W##HO(cur##W##HO);\
    strcpy(x->TYPE, buffer);\
    successMessage();\
}
```

> 这个宏不止会在这一个界面中用到。

## 修改个人信息交互界面的实现

```c
#include "interface/interface.h"

#include <stdio.h>
#include <string.h>

static void modify_passwd() {
    char buffer[MAX_LEN];
    printf("Please input current password: ");
    scanf("%s", buffer);
    User* u = getUser(curUser);
    if (strcmp(u->passwd, buffer)) fail
    printf("Please input new password: ");
    scanf("%s", buffer);
    strcpy(u->passwd, buffer);
    successMessage();
}

make_modify(contact, U, ser)
make_modify(address, U, ser)

static HANDLER handler[] = {modify_passwd, modify_contact, modify_address};
make_interface(M, ODIFY)
```

{% hint style="info" %}
注意修改密码的时候需要用户输入一下当前密码以确认身份。
{% endhint %}

{% hint style="success" %}
到此，我们完成了修改个人信息交互界面的实现。
{% endhint %}

