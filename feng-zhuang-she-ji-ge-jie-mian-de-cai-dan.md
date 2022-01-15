---
description: 正式编写交互界面前的最后一步，封装设计各种菜单。
coverY: 166.40711902113458
cover: .gitbook/assets/menu.jpeg
---

# 📃 封装设计各界面的菜单

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
    |           +- menu  -+- menu.h <---
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
                +- menu  -+- menu.c <---
                |
                +- main.c
```

## 菜单到底在做什么

简单来说，一个菜单做了三件事情：

* 告知用户有哪些选项
* 请用户作出一个选择
* 将用户的选择传给交互界面处理

我们用一个整数值表示用户的选择，菜单的基本效果如下：

![菜单效果预览](.gitbook/assets/pic04.png)

## 一个稚嫩的实现方式

这个效果的实现其实还是蛮简单的：

```c
// menu.c
#include <stdio.h>

int mainMenu() {
    printf("\
\t\t\tMain Menu\n\
+==============+================+===============+========+\n\
| 1.User Login | 2.User Sign Up | 3.Admin Login | 4.Exit |\n\
+==============+================+===============+========+\n");
    printf("Please choose operation:");
    int res;
    scanf("%d", &res);
    return res;
}
```

上面就是主菜单的一个简单实现了，当我们写交互界面的时候只需要调用这个函数，就可以知道用户选择了第几个选项，然后选择相应的处理函数（这个处理函数需要自己来实现）来处理这个选择就可以。

但此时我们发现了两个问题：

*   用户的非法输入

    > 如果用户不听话，输入了比1小或者比4大的数字怎么办？
    >
    > 如果用户更狂野一点，输入了`3.1415926535`（小数）怎么办？
    >
    > 如果用户疯了，输入了“F\* You!"（字符串）怎么办？
*   有很多类似的菜单

    > 每个菜单函数的内容都差不多，只不过是提示信息和选项个数变了。
    >
    > 我们需要每个菜单都写一个，总计需要写8遍几乎重复的函数吗？

## 优化：健体+瘦身

### “健体”优化：处理用户的非法输入，提升鲁棒性

处理用户非法输入的基本思路是：

* 打印提示信息告诉用户它的输入是非法的
* 给它重新输入的机会，直到遇到合法输入为止

所以具体实现上可以这样写：

```c
// menu.c
#include "tools/hint.h"

#include <stdio.h>
#include <stdlib.h>

int mainMenu() {
    printf("\
\t\t\tMain Menu\n\
+==============+================+===============+========+\n\
| 1.User Login | 2.User Sign Up | 3.Admin Login | 4.Exit |\n\
+==============+================+===============+========+\n");
    printf("Please choose operation:");
    char buffer[MAX_LEN];
    int res;
    scanf("%s", buffer);
    res = atoi(buffer);
    while (1) {
        if (res >= 1 && res <= 4) break;
        illegalMessage();
        printf("Please try again: ");
        scanf("%s", buffer);
        res = atoi(buffer);
    }
    return res;
}
```

因为无论用户输入什么我们都可以认为是字符串，然后利用`atoi`函数将字符串转化成整型。如果转化失败或者转化的整型不在我们的选项范围内，则说明用户输入非法，我们用一个循环让用户再次输入，直到输入合法之后才能跳出循环。那么我们就解决了用户哥哥胡乱输入的问题。

{% hint style="info" %}
这种用字符串装载用户输入，之后自己处理字符串的方式在处理用户输入上面其实还是挺常用的，可以稍微注意一下。使用循环的方式给用户多次输入的机会也是很常用的手法。

比如说`ssh`服务有3次输入用户名和密码的机会，其实就是用类似于这样的循环来做的。
{% endhint %}

{% hint style="warning" %}
这里用到了`stdlib.h`头文件里面的`atoi`函数，如果不清楚这个函数的用法的可以寻找相关教程了解一下。

参考教程： [https://www.runoob.com/cprogramming/c-function-atoi.html](https://www.runoob.com/cprogramming/c-function-atoi.html)

之后的内容中使用到这个函数的时候笔者就不再重复了。
{% endhint %}

### “瘦身”优化：泛化编程，一个函数写完所有的菜单

用一个函数写，难道是一大坨`if-else`？当然不是。

我们现预览一下，整个项目中所需要的菜单结构如下：

![](.gitbook/assets/pic05.png)

> 其实这也是我们之后编写交互界面的结构。

我们用一个枚举类型表示这8类不同的菜单：

```c
// menu.h
// 菜单类型
typedef enum {MAIN, ADMIN, USER, BUYER, SELLER, INFO, MODIFY, GOOD} Menu;
```

下面我们希望完成这样一个函数：

```c
// menu.h

/**
 * @brief 菜单的作用是给出提示信息并获取用户输入
 * 
 * @param type 菜单类型
 * @return int 用户的有效输入
 */
int menu(Menu type);
```

我们知道，枚举类型的本质其实就是无符号整数`unsigned int`，所以我们可以很容易的利用数组结构建立起每种菜单类型和菜单提示信息之间的映射关系。

{% hint style="info" %}
这个地方利用数组结构建立枚举类型和字符串之间映射关系的手法其实在good模块中建立state和stateName时已经用过一次了，这里提醒一下，因为之后还会用到类似的手法。
{% endhint %}

在此之前，我们先在config.h里面约定好一共有8个菜单：

```c
// config.h
#define MENU_NUM 8
```

然后开始愉快的打表之旅：

> 其实这个表有一个高级的名字叫做哈希表（当然这个地方它哈希表的特性表现的不明显）。

```c
// menu.c
#include "config.h"

// 不同菜单的提示信息
static const char* menuPrompts[MENU_NUM] = {"\
+==============+================+===============+========+\n\
| 1.User Login | 2.User Sign Up | 3.Admin Login | 4.Exit |\n\
+==============+================+===============+========+\n", \
"\
+=========+==========+==========+=========+==========+=======+========+\n\
| 1.Goods | 2.Search | 3.Orders | 4.Users | 5.Delete | 6.Ban | 7.Back |\n\
+=========+==========+==========+=========+==========+=======+========+\n", \
"\
+=========+==========+==============+========+\n\
| 1.Buyer | 2.Seller | 3.Infomation | 4.Back |\n\
+=========+==========+==============+========+\n", \
"\
+=========+=======+==========+=========+===============+========+\n\
| 1.Goods | 2.Buy | 3.Search | 4.Order | 5.Information | 6.Back |\n\
+=========+=======+==========+=========+===============+========+\n", \
"\
+========+=========+==========+=======+=========+========+\n\
| 1.Sell | 2.Goods | 3.Modify | 4.Ban | 5.Order | 6.Back |\n\
+========+=========+==========+=======+=========+========+\n", \
"\
+===============+==========+==========+========+\n\
| 1.Information | 2.Modify | 3.Top Up | 4.Back |\n\
+===============+==========+==========+========+\n", \
"\
+============+===========+===========+========+\n\
| 1.Password | 2.Contact | 3.Address | 4.Back |\n\
+============+===========+===========+========+\n", \
"\
+========+=========+===============+========+\n\
| 1.Name | 2.Price | 3.Description | 4.Back |\n\
+========+=========+===============+========+\n"};

// 不同菜单的名称
static const char* menuNames[MENU_NUM] = {"Main", "Admin", "User", "Buyer", "Seller", "Information", "Modify", "Good"};
```

这样以来，我们就可以通过`menuPrompts[MAIN]`来获取主菜单的提示信息，通过`menuNames[MAIN]`来获取主菜单的名称。

所以我们可以编写一个针对不同类型的菜单打印不同类型提示信息的函数来作为我们最终菜单函数的辅助函数：

```c
// menu.c
#include "menu/menu.h"
#include "tools/color.h"

#include <stdio.h>

/**
 * @brief 打印提示信息
 * 
 * @param type 菜单类型
 */
static void promptMessage(Menu type) {
    printf("\t\t\t%s%s Menu%s\n", REVERSE, menuNames[type], RESET);
    printf("%s\nPlease choose operation: ", menuPrompts[type]);
}
```

下面还有一个问题就是每种类型的菜单选项个数是不一样的，所以我们怎么获取每种菜单的选项个数呢？

难道是一大坨`if-else`？当然不是。（哈哈，梅开二度）

相信你也猜到了，还是打表：

```c
// menu.c
#include "menu/menu.h"
#include "config.h"

const int optionNum[MENU_NUM] = {4, 7, 4, 6, 6, 4, 4, 4};
```

相信你也注意到了，这里没有加`static`关键字，其实是因为后面的交互界面模块需要用到这个`optionNum`，因为我们交互界面需要写多少个处理函数是从这里得到的。

此外，我们约定每种类型的菜单的最后一项都默认是返回或者退出。这个部分我们先按下不表，在后续编写交互界面的时候你就懂了。

我们暂时承认这样的设计，在`menu.h`中加上这个数组的声明：

```c
// menu.h
// 不同菜单的选项数目
extern const int optionNum[];
```

综上所属，我最终给出的实现如下：

## 最终实现

```c
#include "menu/menu.h"
#include "tools/hint.h"
#include <stdio.h>
#include <stdlib.h>

int menu(Menu type) {
    promptMessage(type);
    char buffer[MAX_LEN];
    int res;
    scanf("%s", buffer);
    res = atoi(buffer);
    while (1) {
        if (res >= 1 && res <= optionNum[type]) break;
        illegalMessage();
        printf("Please try again: ");
        scanf("%s", buffer);
        res = atoi(buffer);
    }
    return res;
}
```

最后我们把一个漂亮的函数声明放在头文件里面供其他模块调用就可以了，注意不要忘了添上美美的注释哈。

```c
// menu.h

/**
 * @brief 菜单的作用是给出提示信息并获取用户输入
 * 
 * @param type 菜单类型
 * @return int 用户的有效输入
 */
int menu(Menu type);
```

{% hint style="success" %}
到此，我们封装好了一个美美的菜单函数供外部模块十分优雅而又简单得调用了。夸一下自己的代码好漂亮，我们可以进入下一站了。
{% endhint %}



