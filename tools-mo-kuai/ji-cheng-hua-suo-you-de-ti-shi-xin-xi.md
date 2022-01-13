---
description: 将所有的提示信息集中在hint模块里面。
---

# 📓 集成化所有的提示信息

在这个部分我们需要思考的是整个项目中到底有哪些提示信息，我总结的大概有如下几种：

*   欢迎信息

    > 虽然只用了一次，不过我还是放在了这个模块里。
* 正在载入
* 操作成功
* 操作失败
* 非法输入
*   无效实现

    > 这个信息在最终的产品中是不应该出现的，它只在开发的过程中，如果某个部分的功能暂时实现不了，以后才能实现。但是为了搭建模块的需要，又不得不先写点什么的话，可以放置一个`invalidMessage()`作为权宜之计。

所以`hint.h`的内容如下：

```c
// hint.h
#ifndef _HINT_H_
#define _HINT_H_

void welcomeMessage();
void successMessage();
void failureMessage();
void illegalMessage();
void loadingMessage();
void exitingMessage();
void invalidMessage();

#endif
```

{% hint style="info" %}
在实现的过程中，可以给不同的信息加上不同的颜色（比如说操作成功加上绿色，操作失败加上红色，加载中加上蓝色之类的），还可以用字符画的方式实现某些信息，以使得界面更加美观。

生成字符画推荐一个网站： [http://patorjk.com/software/taag/#p=display\&f=Big\&t=Whale%20Market%0A](http://patorjk.com/software/taag/#p=display\&f=Big\&t=Whale%20Market%0A)
{% endhint %}

{% hint style="warning" %}
先思考一下自己将会如何实现，再看我的实现。
{% endhint %}

我的实现如下：

```c
// hint.c

#include "tools/hint.h"
#include "tools/color.h"
#include <stdio.h>

// all kinds of visual hints
static const char* BANNER = "\
\t__          ___           _          __  __            _        _       _\n\
\t\\ \\        / / |         | |        |  \\/  |          | |      | |     | |\n\
\t \\ \\  /\\  / /| |__   __ _| | ___    | \\  / | __ _ _ __| | _____| |_    | |\n\
\t  \\ \\/  \\/ / | '_ \\ / _` | |/ _ \\   | |\\/| |/ _` | '__| |/ / _ \\ __|   | |\n\
\t   \\  /\\  /  | | | | (_| | |  __/   | |  | | (_| | |  |   <  __/ |_    |_|\n\
\t    \\/  \\/   |_| |_|\\__,_|_|\\___|   |_|  |_|\\__,_|_|  |_|\\_\\___|\\__|   (_)\n\
";

static const char* SUCCESS = "\t\t*** Operation Successful! ***";
static const char* FAILURE = "\t\t*** Operation Failed! ***";
static const char* ILLEGAL = "\t\t*** Illegal Input! ***";
static const char* LOADING = "\t\t*** Loading ... ***";
static const char* EXITING = "\t\t*** Exiting ... ***";

static const char* INVALID = "\
\t  _____                           _   _       _   _\n\
\t |_   _|                         | | (_)     | | | |\n\
\t   | |    _ __   __   __   __ _  | |  _    __| | | |\n\
\t   | |   | '_ \\  \\ \\ / /  / _` | | | | |  / _` | | |\n\
\t  _| |_  | | | |  \\ V /  | (_| | | | | | | (_| | |_|\n\
\t |_____| |_| |_|   \\_/    \\__,_| |_| |_|  \\__,_| (_)\n\
";

void welcomeMessage() {
    printf("\n%s%sWelcome to%s\n", BOLD, FRONT_RED, RESET);
    printf("%s%s%s%s\n", FRONT_BLUE, BOLD, BANNER, RESET);
    printf("%sThis is WinterCode Project for C-Beginners.%s\n\n", FRONT_PURPLR, RESET);
}

void successMessage() { printf("\n%s%s%s\n\n", FRONT_GREEN, SUCCESS, RESET); }
void failureMessage() { printf("\n%s%s%s\a\n\n", FRONT_RED, FAILURE, RESET); }
void illegalMessage() { printf("\n%s%s%s\a\n\n", FRONT_RED, ILLEGAL, RESET); }
void loadingMessage() { printf("\n%s%s%s\n\n", FRONT_BLUE, LOADING, RESET); }
void exitingMessage() { printf("\n%s%s%s\n\n", FRONT_RED, EXITING, RESET); }
void invalidMessage() { 
    printf("\n%s%s%s\n\n", FRONT_RED, INVALID, RESET);
    printf("\n%s%sThis Function is Waiting For you to Implement ...%s\n\n", \
FRONT_RED, BOLD, RESET);
}

```

我们知道，栈区的空间是很小的（相对来讲），所以较大的数据（比如说大字符串），我们会选择放在静态区或者动态区。

并且这些数据（字符串）从程序开始到程序结束都是一直需要的，所以我通过全局常量的方式将他们放在了静态区。

将数据放置在静态区的方法有两种：

* 全局变量/全局常量
* &#x20;`static`关键字限制的局部变量

此外，这些字符串只在本模块里面发挥作用，外部模块只需要调用本模块头文件所提供的接口message函数就足够了，所以我并没有在头文件里面声明这些常量。

同时，我还使用了`stati`关键字将他们的文件作用域限制在了本模块内。这样一来，哪怕外部模块中声明了这些字符串常量，外部模块也不能实用此处的定义，只能使用别处的定义（如果别处有的话，否则编译器会报错）。

{% hint style="info" %}
上述设计其实目的只有一个，加强模块的抽象程度与封装程度。外部模块除非调用本模块对外声明好的接口这一种途径以外，不应当有任何其他途径来实用本模块内定义的内容。

这样设计出来的模块才是风格良好且封装完全的，同时也会更加安全，不容易出错。
{% endhint %}

{% hint style="warning" %}
上述设计涉及到了C语言中变量作用域以及生存期的相关知识，如果这部分知识点还不够清晰的话，可能需要先找一些相关教程复习一下，因为在我的整个设计当中会经常用到C语言的这些特性。在之后的教程中，如非必要，我将不再解释该知识点。
{% endhint %}

