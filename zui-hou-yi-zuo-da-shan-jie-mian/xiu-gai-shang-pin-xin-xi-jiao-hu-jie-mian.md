---
description: 修改商品信息交互界面的实现
---

# 🐿 修改商品信息交互界面

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
    |           +- interface -+- interface.h
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
                |            -+- modifyInterface.c
                |            -+- sellerInterface.c
                |            -+- goodInterface.c <---
                |            -+- buyerInterface.c
                |
                +- main.c
```

## 修改商品信息交互界面的实现

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

{% hint style="success" %}
至此，我们完成了了修改商品信息交互界面的实现。
{% endhint %}

