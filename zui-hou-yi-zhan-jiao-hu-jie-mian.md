---
description: 到此我们已经砖瓦皆备，只需彩线串珠了。
---

# 🖥 最后一站——交互界面

## 引子

我们已经做了许多添砖加瓦的工作，下面我们只需要把这些部分都组合起来就可以了。

不过，行百里者半九十，这一部分也是最花时间的一部分，因为这一部分需要一直保持一个整体的宏观把控，不能乱了阵脚。

交互界面适合菜单一一对应的：

![交互界面结构示意图](.gitbook/assets/pic05.png)

不过在实际的开发过程中我们往往是先有顶层的架构，再添砖加瓦。因为我的教程是在读者已经有了一个自己版本的基础上写的，所以采用了自下而上的顺序，这样教程写起来会轻松一些。

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
                +- interface -+- mainInterface.c <---
                |            -+- adminInterface.c <---
                |            -+- userInterface.c <---
                |            -+- infoInterface.c <---
                |            -+- modifyInterface.c <---
                |            -+- sellerInterface.c <---
                |            -+- goodInterface.c <---
                |            -+- buyerInterface.c <---
                |
                +- main.c
```

## 交互界面到底在做什么

交互界面主要做工作流程如下：

* 通过菜单获取用户输入
*   根据用户输入选择对应的处理函数

    > 这里的处理函数有可能是下一级交互界面。
* 重复上述步骤，直到用户选择退出这一级界面

根据上述内容，我们实现一个稚嫩的主菜单。

## 一个稚嫩的主菜单

```c
// mainInterface.c
#include "interface/interface.h"
#include "tools/hint.h"
#include "menu/menu.h"

static void USER_Login() { ... }
static void USER_SignUp() { ... }
static void ADMIN_Login() { ... }

void MAIN_Interface() {
    successMessage(); // 成功进入主菜单
    int op = menu(MAIN); // 使用菜单获取用户输入
    while (op != optionNum[MAIN]) {
    // 如果用户不选择退出（默认都是最后一个选项是退出）
        loadingMessage();
        if (op == 1) {
            USER_Login(); // 用户登陆
        }
        else if (op == 2) {
            USER_SignUp(); // 用户注册
        }
        else if (op == 3) {
            ADMIN_Login(); // 管理员登陆
        }
        op = menu(MAIN); 
    } 
    loadingMessage(); 
    successMessage();
}
```

其实按照上面的实现思路，一部分一部分慢慢写，已经可以完成这个项目的全部功能了。但这毕竟是个教学性质的项目，我希望自己的代码稍微漂亮一点。

> 言下之意就是实际写代码的时候能跑就行，也不管多丑陋或者多难扩展，哈哈。

上面的代码有如下一些可以优化的地方：

* 一大坨`if-else`
* 马上至少要写8遍几乎重复的代码

> 有没有发现和我们之前写menu的时候遇到的丑陋一摸一样，不过这次的处理方式不一样了。

### 优化1：处理丑陋的`if-else`

这里，我们采用的方式和之前`menu`的方式一样——哈希表（其实就是打表格，建立映射关系）。

但之前我们是把字符串放在数组里面的，现在我们需要建立的是整数和函数之间的关系，函数可以放在数组里面吗？

其实是可以的，我使用了函数指针，首先在interface.h中

