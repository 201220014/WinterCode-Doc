---
description: 画龙点睛的时刻到啦！
---

# 🚪 最终章——程序入口

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
恭喜你，经过这次项目，相信你已经完成了从C-Beginner到C-Advanced-Beginner的蜕变，愿你前途坦然，bug不再。
{% endhint %}

