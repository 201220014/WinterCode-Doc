---
description: 实现买家交互界面
---

# 💰 买家交互界面

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
                |            -+- goodInterface.c
                |            -+- buyerInterface.c <---
                |
                +- main.c
```

## 买家交互界面的实现

{% hint style="info" %}
注意：购买操作是整个项目中牵一发而动全身的操作，需要仔细考虑其逻辑：

* 判断是否可以购买
  * 已出售、已下架不能买
  * 余额不足不能买
* 买家扣钱，卖家加钱
* 生成订单
{% endhint %}

```c
#include "interface/interface.h"

#include <stdio.h>
#include <stdlib.h>
#include <string.h>

static void buy() {
    char buffer[MAX_LEN];
    printf("Please input Good id to buy: ");
    scanf("%s", buffer);
    int idx = searchGoodID(buffer);
    if (idx == -1) fail

    Good* g = getGood(idx);
    User* u = getUser(curUser); // buyer

    if (g->state != SELLING) fail
    if (g->price > u->balance) fail
    
    u->balance -= g->price;
    g->state = SOLD;
    userTopUp(g->seller_id, g->price);

    // generate an order
    Order* o = (Order*)malloc(sizeof(Order));
    strcpy(o->good_id, g->id);
    strcpy(o->seller_id, g->seller_id);
    strcpy(o->buyer_id, u->id);
    o->price = g->price;

    if (addOrder(o)) successMessage();
    else failureMessage();
    free(o);
}

static void info() {
    char buffer[MAX_LEN];
    printf("Please input Good ID for more information: ");
    scanf("%s", buffer);
    int idx = searchGoodID(buffer);
    if (idx == -1) fail
    loadingMessage();
    goodInfo(idx);
    successMessage();
}

make_search(Buyer)

make_my(Goods, B, uyer)
make_my(Orders, B, uyer)

static HANDLER handler[] = {myGoods, buy, search, myOrders, info};
make_interface(B, UYER)
```

{% hint style="success" %}
至此，我们已经完成了所有的交互界面了。夸一下自己。:smile:
{% endhint %}

