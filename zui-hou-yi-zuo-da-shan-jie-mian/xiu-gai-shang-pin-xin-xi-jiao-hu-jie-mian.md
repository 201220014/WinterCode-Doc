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
#include <stdlib.h>

int curGood = -1;

make_modify(name, G, ood)
make_modify(description, G, ood)

static void modify_price() {
    char buffer[MAX_LEN];
    printf("Please input new price: ");
    scanf("%s", buffer);
    check_double
    Good* x = getGood(curGood);
    x->price = m;
    successMessage();
}

static HANDLER handler[] = {modify_name, modify_price, modify_description};
make_interface(G, OOD)
```

{% hint style="success" %}
至此，我们完成了了修改商品信息交互界面的实现。
{% endhint %}

