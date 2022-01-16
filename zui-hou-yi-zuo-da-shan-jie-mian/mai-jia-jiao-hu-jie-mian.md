---
description: 卖家交互界面的实现
---

# 🎐 卖家交互界面

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
                |            -+- modifyInterface.c
                |            -+- sellerInterface.c <---
                |            -+- goodInterface.c
                |            -+- buyerInterface.c
                |
                +- main.c
```

## 添加辅助宏

```c
// interface.h

#define make_my(TYPE, W, HO) \
static void my##TYPE() {\
    print##TYPE##4##W##HO(getUser(curUser)->id);\
    successMessage();\
}
```

## 卖家交互界面实现

```c
// sellerInterface.c
#include "interface/interface.h"

#include <stdio.h>
#include <stdlib.h>
#include <string.h>

static void sell() {
    User* seller = getUser(curUser);
    Good* g = (Good*)malloc(sizeof(Good));
    printf("Please input the information of your product ...\n");
    printf("Name: ");
    scanf("%s", g->name);
    printf("Price: ");
    char buffer[MAX_LEN];
    scanf("%s", buffer);
    check_double
    g->price = m;
    printf("Description: ");
    scanf("%s", g->description);
    strcpy(g->seller_id, seller->id);
    if (addGood(g)) successMessage();
    else failureMessage();
    free(g);
}

static void modify() {
    printf("Please input the good ID to modify: ");
    char buffer[MAX_LEN];
    scanf("%s", buffer);
    curGood = searchGoodID(buffer);
    if (curGood == -1) fail
    User* u = getUser(curUser);
    Good* g = getGood(curGood);
    if (strcmp(u->id, g->seller_id) == 0 && g->state == SELLING) 
        GOOD_Interface();
    else failureMessage();
}

make_my(Goods, S, eller)
make_my(Orders, S, eller)

make_ban(G, ood, "Good", getUser(curUser)->id);

static HANDLER handler[] = {sell, myGoods, modify, banGood, myOrders};
make_interface(S, ELLER)
```

关于修改部分，我们需要判断是否具有修改权限，判断没有问题的话需要将当前的商品下标存下来，以供修改模块使用。

所以我们在`goodInterface.c`里面定义一个变量：

```c
// goodInterface.c

int curGood = -1; // 初始值-1表示不存在
```

并在`interface.h`中声明这个变量：

```c
// interface.h

extern int curGood; 当前商品
```

{% hint style="success" %}
至此，我们完成了卖家交互界面的实现。
{% endhint %}

