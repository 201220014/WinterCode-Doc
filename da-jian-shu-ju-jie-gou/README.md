---
description: 为之后交互界面的编写做准备。
cover: >-
  https://images.unsplash.com/photo-1640861694386-4e5ff638e6b7?crop=entropy&cs=srgb&fm=jpg&ixid=MnwxOTcwMjR8MHwxfHJhbmRvbXx8fHx8fHx8fDE2NDIyNzE3MDg&ixlib=rb-1.2.1&q=85
coverY: 353.7096774193548
---

# 🎋 搭建数据结构

在这个环节里面，我们会搭建本次project所需要的所有的数据结构。搭建步骤基本如下：

*   定义数据类型

    > 我选择用结构体来定义单体数据的类型。
*   选择数据结构

    > 我选择了数组作为储存单体数据的数据结构，当然，这样选择需要我们约定一下最大数据规模。这个约定将在后续内容中陈述。
*   设计对外接口

    > **裸露的数据结构是不可以直接给外部模块使用的**，这样在实际开发过程中会很危险。所以我们在定义数据结构的时候，需要隐藏内部实现，将可以进行的操作封装起来，以供外部模块调用。

## 我们需要实现的模块

```c
.---+- include -+- tools -+- color.h
    |           |         +- hint.h
    |           |         +- info.h
    |           | 
    |           +- user  -+- user.h <---
    |           |
    |           +- good  -+- good.h <---
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
                |         +- user.txt <---
                |         +- good.txt <---
                |         +- order.txt <---
                |
                +- user  -+- user.c <---
                |
                +- good  -+- good.c <---
                |
                +- order -+- order.c <---
                |
                +- main.c
```

{% hint style="success" %}
至此，你应该对怎样编写一个数据结构模块有了初步的认识，下面让我们正式进入数据模块的搭建吧！
{% endhint %}

