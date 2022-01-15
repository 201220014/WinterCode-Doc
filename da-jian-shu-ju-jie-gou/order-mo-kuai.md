---
description: 搭建储存和操作订单信息的底层数据结构
---

# 📁 Order模块

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
    |           +- order -+- order.h <---
    |           |
    |           +- config.h <---
    |
    +-   src   -+- tools -+- color.c
                |         +- hint.c
                |         +- info.c 
                |
                +- data  -+- id.txt
                |         +- user.txt
                |         +- good.txt 
                |         +- order.txt <---
                |
                +- user  -+- user.c 
                |
                +- good  -+- good.c
                |
                +- order -+- order.c <---
                |
                +- main.c
```

{% hint style="info" %}
有了前面两个模块设计的铺垫，这个模块的编写过程也就很顺利了，所以这里不作过多的解释，直接贴代码了。
{% endhint %}

## 模块头文件接口设计

```c
#ifndef _ORDER_H_
#define _ORDER_H_

#include "config.h"

// type definition of order
typedef struct {
    char id[MAX_LEN];
    char good_id[MAX_LEN];
    double price;
    char date[MAX_LEN];
    char seller_id[MAX_LEN];
    char buyer_id[MAX_LEN];
} Order;

/**
 * @brief pull all order infomation from external file
 * 
 */
void pullOrders();

/**
 * @brief push all order information into external file
 * 
 */
void pushOrders();

/**
 * @brief add Order o to orderss
 * 
 * @param o order to be added
 * @return int 0 if failed else 1
 */
int addOrder(Order* o);

/**
 * @brief print all orders' information
 * 
 */
void printOrders();

/**
 * @brief print orders with seller id
 * 
 * @param id seller id
 */
void printOrders4Seller(const char* id);

/**
 * @brief print orders with buyer id
 * 
 * @param id buyer id
 */
void printOrders4Buyer(const char* id);

#endif
```

## 模块源文件实现

```c
#include "order/order.h"
#include "tools/info.h"
#include <stdio.h>
#include <string.h>

static Order orders[MAX_ORDER]; // all orders
static int totalOrder = 0;

static const char* filePath = "src/data/order.txt";
static const char* header = "|ID         |Good       |Price      |Date       |Seller     |Buyer      |";
static const char* divide = "+-----------+-----------+-----------+-----------+-----------+-----------+";

void pullOrders() {
    totalOrder = 0;
    FILE* pf = fopen(filePath, "r");
    if (pf) {
        while (fscanf(pf, "%s%s%lf%s%s%s", orders[totalOrder].id, \
orders[totalOrder].good_id, &orders[totalOrder].price, orders[totalOrder].date, \
orders[totalOrder].seller_id, orders[totalOrder].buyer_id) != EOF) totalOrder++;
        fclose(pf);
    }
}

void pushOrders() {
    FILE* pf = fopen(filePath, "w");
    for (int i = 0; i < totalOrder; i++)
        fprintf(pf, "%s %s %.1f %s %s %s\n", orders[i].id, \
orders[i].good_id, orders[i].price, orders[i].date, \
orders[i].seller_id, orders[i].buyer_id);
    fclose(pf);
}

int addOrder(Order* o) {
    if (totalOrder == MAX_ORDER) return 0;
    genID(o->id, 'O');
    getDate(o->date);
    orders[totalOrder++] = *o;
    return 1;
}

static void printOrder(int i) {
    printf("|%-10s |%-10s |%-10.1f |%-10s |%-10s |%-10s |\n", orders[i].id, \
orders[i].good_id, orders[i].price, orders[i].date, \
orders[i].seller_id, orders[i].buyer_id);
}

void printOrders() {
    print_header
    for (int i = 0; i < totalOrder; i++) {
        printOrder(i);
        print_divide
    }
}

void printOrders4Seller(const char* id) {
    print_header
    for (int i = 0; i < totalOrder; i++)
        if (strcmp(orders[i].seller_id, id) == 0) {
            printOrder(i);
            print_divide
        }
}

void printOrders4Buyer(const char* id) {
    print_header
    for (int i = 0; i < totalOrder; i++)
        if (strcmp(orders[i].buyer_id, id) == 0) {
            printOrder(i);
            print_divide
        }
}
```

{% hint style="success" %}
至此，我们搭建好了整个项目所需的所有底层数据结构，并且作了充分的封装。

> 实际开发过程中的封装一般不会一开始就是充分的，在后续的过程中可能要很多次得回头完善这些数据结构。

砖瓦皆备，现在可以稍微休息一下，我们下面要开始建房子了。
{% endhint %}
