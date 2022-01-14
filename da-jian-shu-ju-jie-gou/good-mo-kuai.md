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

{% hint style="info" %}
其实商品模块和之前用户模块的设计过程是类似的，读者可以先尝试自行设计，有了一个自己的版本之后再来参考我的写法，可能会更有学习效果一些。
{% endhint %}

## 设计数据类型

商品有三种状态：销售中、已售出、已下架。我们用一个枚举类型表示商品的状态：

```c
// good.h
// 商品状态
typedef enum {SELLING, SOLD, BANNED} State;
```

{% hint style="warning" %}
如果对于枚举类型已经不太熟悉的话，可以寻找相关教程复习一下。

参考教程： [https://www.runoob.com/cprogramming/c-enum.html](https://www.runoob.com/cprogramming/c-enum.html)

后续内容还会涉及到枚举类型的使用，将不再额外说明。
{% endhint %}

根据前面所说的要求，我么可以在good`.h`中定义如下的用户类型：

```c
// good.h
#include "config.h"

// 商品类型定义
typedef struct {
    char id[MAX_LEN];
    char name[MAX_LEN];
    double price;
    char seller_id[MAX_LEN];
    State state;
    char date[MAX_LEN];
    char description[MAX_LEN];
} Good;
```

## 选择数据结构

我选用了数组结构，因此我们需要约定商品的总数不超过`MAX_GOOD`，并且在`config.h`中定义这个宏。

```c
// config.h
#define MAX_GOOD    128
```

我们在`good.c`中定义这个数据结构，同时定义商品的初始总数为0：

```c
#include "good/good.h"

static Good goods[MAX_GOOD]; // all goods
static int totalGood = 0;
```

## 设计对外接口

### 接口设计

根据项目刚开始时的需求汇总，我们对于商品的数据结构提供如下一些接口，声明在`good.h`头文件中。

```c
// good.h

/**
 * @brief 从外部文件导入商品信息
 * 
 */
void pullGoods();

/**
 * @brief 将商品信息导出到外部文件
 * 
 */
void pushGoods();

/**
 * @brief 获取商品对象
 * 
 * @param idex 商品在的数组下标
 * @return User* 指向对应商品的指针
 */
Good* getGood(int idex);

/**
 * @brief 添加新商品
 * 
 * @param u 待添加的商品指针
 * @return 添加成功返回1，添加失败返回0
 */
int addGood(Good* g);

/**
 * @brief 查找给定ID的商品
 * 
 * @param id 需要查找的商品ID
 * @return int 成功返回下标，失败返回-1
 */
int searchGoodID(const char* id);

/**
 * @brief 删除给定ID的商品
 * 
 * @param id 待删除商品的id
 * @param who 删除操作执行者的id，管理员为null
 * @return int 删除成功返回1，失败返回0
 */
int deleteGood(const char* id, const char* who);

/**
 * @brief 打印详细的商品信息
 * 
 * @param i 商品的数组下标
 */
void goodInfo(int i);

/**
 * @brief 打印所有商品的信息
 * 
 */
void printGoods();

/**
 * @brief 为买家搜索商品-只搜索正在出售中的商品
 * 
 * @param name 待搜索商品的名称
 */
void searchGoodName4Buyer(const char* name);

/**
 * @brief 为管理员搜索商品-搜索所有商品
 * @param name 待搜索商品的名称
 */
void searchGoodName4Admin(const char* name);

/**
 * @brief 打印给定卖家id的商品
 * 
 * @param id 需要打印商品的卖家id
 */
void printGoods4Seller(const char* id);

/**
 * @brief 打印出售中的商品
 * 
 */
void printGoods4Buyer();
```

{% hint style="info" %}
这里我们注意到不同的主题查看商品的权限是不一样的，比如说：

* 管理员可以查看全部的商品（包括已下架的）
* 买家只能查看正在销售中的商品
* 卖家只能查看自己出售的商品
{% endhint %}

### 接口实现

#### 从外部文件导入商品信息

```c
// good.c
#include "good/good.h"

#include <stdio.h>

void pullGoods() {
    totalGood = 0;
    FILE* pf = fopen(filePath, "r");
    if (pf) {
        while (fscanf(pf, "%s%s%lf%s%u%s%s", goods[totalGood].id, \
goods[totalGood].name, &goods[totalGood].price, goods[totalGood].seller_id, \
&goods[totalGood].state, goods[totalGood].date, goods[totalGood].description) != EOF) totalGood++;
        fclose(pf);
    }
}
```

> 注意，枚举类型本质上是无符号整数。

#### 将商品信息导出到外部文件

```c
// good.c
#include "good/good.h"

#include <stdio.h>

void pushGoods() {
    FILE* pf = fopen(filePath, "w");
    for (int i = 0; i < totalGood; i++)
        fprintf(pf, "%s %s %.1f %s %u %s %s\n", goods[i].id, goods[i].name, goods[i].price, \
goods[i].seller_id, goods[i].state, goods[i].date, goods[i].description);
    fclose(pf);
}
```

#### 获取商品对象

```c
Good* getGood(int idex) { return goods + idex; }
```

#### 打印详细的商品信息

```c
// good.c
#include "good/good.h"

#include <stdio.h>

void goodInfo(int i) {
    printf("ID:            | %s\n", goods[i].id);
    printf("Name:          | %s\n", goods[i].name);
    printf("Description:   | %s\n", goods[i].description);
    printf("Date:          | %s\n", goods[i].date);
    printf("Price:         | %.1f\n", goods[i].price);
    printf("Seller:        | %s\n", goods[i].seller_id);
    printf("State:         | %s\n", stateName[goods[i].state]);
}
```

#### 添加新商品

```c
// good.c
#include "good/good.h"
#include "tools/info.h"

int addGood(Good* g) {
    if (totalGood == MAX_GOOD) return 0;
    genID(g->id, 'G');
    getDate(g->date);
    g->state = SELLING;
    goods[totalGood++] = *g;
    return 1;
}
```

> 注意判断是否数组已满，防止数组越界导致程序崩溃。

#### 查找给定ID的商品

```c
// good.c
#include "good/good.h"

#include <string.h>

int searchGoodID(const char* id) {
    for (int i = 0; i < totalGood; i++)
        if (strcmp(goods[i].id, id) == 0) return i;
    return -1;
}
```

> 这里的搜索采用的是最直白的顺序搜索，其实基于顺序表的搜索可以采用折半搜索来提高效率。不过我们这个项目侧重点不在此就是了，所以直接顺序搜索也没问题。

#### 删除给定ID的商品

```c
// good.c
#include "good/good.h"

#include <string.h>

int deleteGood(const char* id, const char* who) {
    int idx = searchGoodID(id);
    if (idx == -1) return 0;
    if (who && strcmp(goods[idx].seller_id, who)) return 0;
    goods[idx].state = BANNED;
    return 1;
}
```

{% hint style="info" %}
这里需要注意以下的是，删除商品并不是真的删除商品的所有信息，而是将商品的状态从销售中或者已售出改为已下架。
{% endhint %}

#### 打印详细的商品信息

```c
// good.c
#include "tools/info.h"
#include "good/good.h"

#include <stdio.h>

static const char* filePath = "src/data/good.txt";
static const char* header = "|ID         |Name       |Price      |Date       |Seller     |State      |";
static const char* divide = "+-----------+-----------+-----------+-----------+-----------+-----------+";
static const char* stateName[] = {"Selling", "Sold", "Banned"};

/**
 * @brief 打印单个商品作为表格的一行
 * 
 * @param i 用户下标
 */
static void printGood(int i) {
    printf("|%-10s |%-10s |%-10.1f |%-10s |%-10s |%-10s |\n", goods[i].id, goods[i].name, goods[i].price, \
goods[i].date, goods[i].seller_id, stateName[goods[i].state]);
}

void printGoods() {
    print_header
    for (int i = 0; i < totalGood; i++) {
        printGood(i);
        print_divide
    }
}
```



