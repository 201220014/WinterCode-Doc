---
description: 到此我们已经砖瓦皆备，只需彩线串珠了。
---

# 🖥 最后一站——交互界面

我们已经做了许多添砖加瓦的工作，下面我们只需要把这些部分都组合起来就可以了。

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

