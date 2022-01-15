---
description: 主交互界面的实现
---

# 📨 主交互界面

{% hint style="danger" %}
其实到这里，后面的内容基本上就是运用前面搭好的架子以及接口了，涉及的技巧基本上也都在前面出现过了，所以之后的每个交互界面大家都可以先自己实现一个版本，再和我的版本对比，会更有收获哦。
{% endhint %}

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
                |            -+- adminInterface.c
                |            -+- userInterface.c
                |            -+- infoInterface.c
                |            -+- modifyInterface.c
                |            -+- sellerInterface.c
                |            -+- goodInterface.c
                |            -+- buyerInterface.c
                |
                +- main.c
```

## 添加辅助宏

```c
// interface.h

#define get_username_passwd\
    char username[MAX_LEN];\
    char passwd[MAX_LEN];\
    printf ("Username: ");\
    scanf("%s", username);\
    printf ("Password: ");\
    scanf("%s", passwd);

#define make_login(T, YPE, cond)\
static void T##YPE##_Login() {\
    get_username_passwd\
    if (cond) { T##YPE##_Interface(); return ; }\
    failureMessage();\
}
```

## 主交互页面的实现

```c
// mainInterface.c
#include "interface/interface.h"

#include <stdio.h>
#include <stdlib.h>
#include <string.h>

void inv() { 
    invalidMessage(); 
    failureMessage();
}

make_login(U, SER, checkPass(username, passwd, &curUser))
make_login(A, DMIN, strcmp(username, ADMIN_NAME) == 0 && strcmp(passwd, ADMIN_PASS) == 0)

static void USER_SignUp() {
    User* u = (User*)malloc(sizeof(User));
    printf("Please input your username: ");
    scanf("%s", u->name);
    printf("Please input your password: ");
    scanf("%s", u->passwd);
    printf("Please input your phone number: ");
    scanf("%s", u->contact);
    printf("Please input your address: ");
    scanf("%s", u->address);
    u->balance = 0;
    if (addUser(u)) successMessage();
    else failureMessage();
    free(u);
}

static HANDLER handler[] = {USER_Login, USER_SignUp, ADMIN_Login};
make_interface(M, AIN)
```

关于用户登陆的那个部分，我们需要记录当前登陆的到底是哪一个用户，以供后续的交互界面使用。所以，我们需要在`userInterface.c`中定义一个全局变量，记录登陆用户的数组下标：

```c
// userInterface.c

int curUser = -1; // 初始值-1表示不存在
```

记录的方式是利用`checkPass`函数的指针参数返回值（这个点在之前user模块讲过，就不重复了）。

为了让其余的interface模块能够使用这个变量，我们需要在`interface.h`头文件中声明这个变量：

```c
// interface.h
// 当前用户
extern int curUser;
```

{% hint style="success" %}
到此，我们完成了主交互页面的实现。
{% endhint %}

{% hint style="info" %}
此时，我们可以从交互页面的编写当中暂时抽身出来，写一下程序的入口，也就是`main`函数。可以先转到后面写程序入口的那个章节，让自己的程序彻底跑起来，再回头补充那些暂时用`inv`代替的交互界面部分。
{% endhint %}
