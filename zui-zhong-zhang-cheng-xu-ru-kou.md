---
description: 画龙点睛的时刻到啦！
cover: >-
  https://images.unsplash.com/photo-1618438051900-493168c9ea50?crop=entropy&cs=srgb&fm=jpg&ixid=MnwxOTcwMjR8MHwxfHNlYXJjaHw2fHxmaW5hbHxlbnwwfHx8fDE2NDIzMzY5Njk&ixlib=rb-1.2.1&q=85
coverY: 205.3953488372093
---

# 🚪 最终章——程序入口

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
                |            -+- buyerInterface.c
                |
                +- main.c <---
```

## 程序入口

我们回顾一下最开始的我们的整体思路：

![项目框架](.gitbook/assets/pic01.png)

我们程序的主流程应该是这样的：

* 启动时：根据外部文件内容搭建所需的数据结构，数据结构的基本算法操作已经封装完毕
* 运行中：由交互界面负责搭建用户和程序算法之间的桥梁
* 结束前：将数据结构内的数据保存到外部文件中

综上，我们最后的程序入口如下：

```c
#include "interface/interface.h"
#include "tools/hint.h"

int main() {
    pullUsers();
    pullGoods();
    pullOrders();

    welcomeMessage();
    MAIN_Interface();
    exitingMessage();
    
    pushUsers();
    pushGoods();
    pushOrders();
    return 0;
}
```

至此，我们整个`Whale Market`项目就大功告成了：

![最终效果](.gitbook/assets/pic04.png)

程序的参考代码仓库： [https://github.com/201220014/WhaleMarket](https://github.com/201220014/WhaleMarket)

{% hint style="success" %}
恭喜你，经过这次项目，相信你已经完成了从C-Beginner到C-Advanced-Beginner的蜕变。
{% endhint %}

