---
description: 管理员交互界面的实现
---

# 🕹 管理员交互界面

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
                |            -+- adminInterface.c <---
                |            -+- userInterface.c
                |            -+- infoInterface.c
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

#define make_all(T, YPE)\
static void all##T##YPE##s() { print##T##YPE##s(); successMessage(); }

#define make_ban(T, YPE, Name, Who)\
static void ban##T##YPE() {\
    char id[MAX_LEN];\
    printf("Please input %s ID to be banned: ", Name);\
    scanf("%s", id);\
    if (delete##T##YPE(id, Who)) successMessage();\
    else failureMessage();\
}

#define make_search(WHO)\
static void search() {\
    char buffer[MAX_LEN];\
    printf("Please input Good Name to search: ");\
    scanf("%s", buffer);\
    loadingMessage();\
    searchGoodName4##WHO(buffer);\
    successMessage();\
}
```

> 上述宏不止在这一个交互界面中会用到，下次用到笔者就不再提示了。

## 管理员交互界面的实现

```c
// adminInterface.c
#include "interface/interface.h"

#include <stdio.h>

make_all(U, ser)
make_all(G, ood)
make_all(O, rder)

make_ban(U, ser, "User", NULL)
make_ban(G, ood, "Good", NULL)

make_search(Admin)

static HANDLER handler[] = {allGoods, search, allOrders, allUsers, banUser, banGood};
make_interface(A, DMIN)
```

{% hint style="success" %}
到此，我们完成了管理员交互页面的实现。
{% endhint %}

