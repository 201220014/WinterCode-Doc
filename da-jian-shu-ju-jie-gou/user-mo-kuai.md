---
description: 搭建储存和操作用户信息的底层数据结构
---

# 👥 User模块

## 当前进度

```c
.---+- include -+- tools -+- color.h
    |           |         +- hint.h
    |           |         +- info.h
    |           | 
    |           +- user  -+- user.h <---
    |           |
    |           +- config.h <---
    |
    +-   src   -+- tools -+- color.c
                |         +- hint.c
                |         +- info.c 
                |
                +- data  -+- id.txt
                |         +- user.txt <---
                |
                +- user  -+- user.c <---
                |
                +- main.c
```

## 设计数据类型

根据前面所说的要求，我么可以在user.h中定义如下的用户类型：

```c
// user.h

#include "config.h"

// 用户的类型定义
typedef struct {
    char id[MAX_LEN];
    char name[MAX_LEN];
    char passwd[MAX_LEN];
    char contact[MAX_LEN];
    char address[MAX_LEN];
    double balance;
} User;
```

{% hint style="warning" %}
结构体很常用了，不过为了以防万一有用法不清晰的地方，还是附上一个参考教程吧。

参考教程：[https://www.runoob.com/w3cnote/c-structures-intro.html](https://www.runoob.com/w3cnote/c-structures-intro.html)

之后的内容中，笔者将默认读者至少了解了结构体的基本用法。至于结构体和联合体的混合嵌套使用、结构体的位域等其他的高阶技巧有兴趣的可以自行了解。
{% endhint %}

这里，我们引入了一个头文件config.h，这个头文件是储存一些全局设定的，这里我们约定本项目中所有的字符串长度均不超过`MAX_LEN`，我们将其定义在`config.h`中。

```c
// config.h
#define MAX_LEN     64
```

随着程序的不断丰富，我们可能会需要一些其他的全局设定或者约定，我们都可以放在`config.h`中。这样一来，如果之后我们要更改这些约定，只需要修改`config.h`头文件即可。

## 选择数据结构

我选用了数组结构，因此我们需要约定用户的总数不超过`MAX_USER`，并且在`config.h`中定义这个宏。

```c
// config.h
#define MAX_USER    128
```

我们在`user.c`中定义这个数据结构，同时定义用户的初始总数为0：

```c
#include "user/user.h"

static User users[MAX_USER]; // 所有的用户
static int totalUser = 0; // 用户总数
```

这里采用全局数组和全局变量将数据结构储存主存的静态数据区，而不是栈区。因为静态数据区变量的生存期是贯穿程序始终的，并且静态数据的空间会比栈区大。

除此之外，用`static`关键字限制这两个变量的文件作用域为本文件内，这样就只有user模块本身的函数方法可以访问着两个数据结构了。外部模块只有通过本模块提供的方法或者说接口才能读取或者修改数据内容。

这个过程就是模块的**封装和抽象**。

## 设计对外接口

### 接口设计

根据项目刚开始时的需求汇总，我们对于用户的数据结构提供如下一些接口，声明在`user.h`头文件中。

```c
// user.h

/**
 * @brief 从外部文件导入用户信息
 * 
 */
void pullUsers();

/**
 * @brief 将用户信息导出到外部文件
 * 
 */
void pushUsers();

/**
 * @brief 获取用户对象
 * 
 * @param idex 用户在的数组下标
 * @return User* 指向对应用户的指针
 */
User* getUser(int idex);

/**
 * @brief 打印详细的用户信息
 * 
 * @param i 用户的数组下标
 */
void userInfo(int i);

/**
 * @brief 添加新用户
 * 
 * @param u 待添加的用户指针
 * @return 添加成功返回1，添加失败返回0
 */
int addUser(User* u);

/**
 * @brief 删除给定ID的用户
 * 
 * @param id 待删除用户的id
 * @param who 删除操作执行者的id，管理员为null
 * @return int 删除成功返回1，失败返回0
 */
int deleteUser(const char* id, const char* who);

/**
 * @brief 打印所有用户的信息
 * 
 */
void printUsers();

/**
 * @brief 检查用户名和密码是否匹配
 * 
 * @param name 用户名
 * @param passwd 密码
 * @param idx 如果成功，通过指针返回用户的下标
 * @return int 匹配成功返回1，失败返回0
 */
int checkPass(const char* name, const char* passwd, int* idx);

/**
 * @brief 用户充值 
 * 
 * @param id 待充值用户id
 * @param m 充值数额
 * @return 充值成功返回1，失败返回0
 */
int userTopUp(const char* id, double m);
```

{% hint style="info" %}
其实我们设计接口的时候可能并不是一开始就能考虑全面的，往往是先设计一些基本的操作，在项目进行的过程中发现现有的接口还不能满足要求，慢慢往里面加一些别的接口。最终才形成了上面的成形的接口设计。
{% endhint %}

### 接口实现

{% hint style="info" %}
我分项展示各个接口的实现方式的时候会将实现过程中需要用到的头文件都包含以下，不过在实际的`src/user/user.c`文件中同一种头文件只需要包含一次即可。

一般的规范是先包含自定义的头文件，再包含系统头文件。

比如说：

```c
// user.c
#include "user/user.h"
#include "tools/info.h"

#include <stdio.h>
#include <string.h>
```

之后的各个模块不再重复上述内容。
{% endhint %}

#### 从外部文件导入信息

```c
// user.c
#include "user/user.h"

#include <stdio.h>

static const char* filePath = "src/data/user.txt";

void pullUsers() {
    totalUser = 0; // 前面已经定义
    FILE* pf = fopen(filePath, "r");
    if (pf) {
        while (fscanf(pf, "%s%s%s%s%s%lf", users[totalUser].id, \
users[totalUser].name, users[totalUser].passwd, users[totalUser].contact, \
users[totalUser].address, &(users[totalUser].balance)) != EOF) totalUser++;
        fclose(pf);
    }
}
```

> 我们约定，用户的信息储存在文件src/data/user.txt中。另外，别忘了包含`user.h`头文件，包含其相对于`include`文件夹的相对路径。

这里使用`static`关键字，将`filePath`的作用域限制在本模块内部（C语言以`.c`文件作为模块划分的基本单位），这样在其他模块里面就可以继续使用`filePath`这个标识符作为文件路径的常量名了。

这种手法在编写界面模块的时候会体现的淋漓尽致，可以期待一下:smile:。

#### 将用户信息导出到外部文件

```c
// user.c
#include "user/user.h"

#include <stdio.h>

void pushUsers() {
    FILE* pf = fopen(filePath, "w");
    for (int i = 0; i < totalUser; i++)
        fprintf(pf, "%s %s %s %s %s %.1f\n", users[i].id, users[i].name, users[i].passwd, \
users[i].contact, users[i].address, users[i].balance);
    fclose(pf);
}
```

> 为了避免浮点数打印精度不确定导致打印格式不统一的问题，我们约定在本项目中，所有的浮点数保留小数点后1位。哪怕用户输入了更高精度的浮点数，因为打印规格符统一使用`.1f`，故而当该输入被打印到标准输出的时候总是1位小数。同时，在经历一次文件读写之后，实际精度也会变成一位小数。

#### 获取用户对象

```c
User* getUser(int idex) { return users + idex; }
```

{% hint style="warning" %}
这里涉及到了C语言指针相关的知识点，后续的内容中也会涉及指针，所以如果还不太熟悉的话可以寻找教程复习一下。

参考教程：[https://www.runoob.com/w3cnote/c-pointer-detail.html](https://www.runoob.com/w3cnote/c-pointer-detail.html)

后续的内容中笔者将默认读者掌握了指针的含义以及基本运算。
{% endhint %}

#### 打印详细的用户信息

```c
// user.c
#include "user/user.h"

#include <stdio.h>

void userInfo(int i) {
    printf("ID:        | %s\n", users[i].id);
    printf("Name:      | %s\n", users[i].name);
    printf("Contact:   | %s\n", users[i].contact);
    printf("Address:   | %s\n", users[i].address);
    printf("Balance:   | %.1f\n", users[i].balance);
}
```

> 打印内容需要注意对齐和美观。

#### 添加新用户

```c
// user.c
#include "user/user.h"
#include "tools/info.h"

#include <string.h>

/**
 * @brief 根据用户名查找用户
 * 
 * @param name 待查找的用户名
 * @return int 查找成功返回数组下标，失败返回-1
 */
static int searchUserName(const char* name) {
    for (int i = 0; i < totalUser; i++)
        if (strcmp(users[i].name, name) == 0) return i;
    return -1;
}

int addUser(User* u) {
    if (totalUser == MAX_USER) return 0;
    if (searchUserName(u->name) != -1) return 0;
    genID(u->id, 'U');
    users[totalUser++] = *u;
    return 1;
}
```

> 因为这里用到了`genID`函数来生成新用户的ID，所以需要包含`info.h`头文件。当然，用到了字符串处理的库函数，自然也需要包含`string.h`头文件。

这里值得提一下的是，所有的字符串的赋值都不可以直接用等号，而应当使用`strcpy`函数，因为字符串是复杂数据类型，变量中存的只是首地址，而不是字符串本身。不过结构体是可以直接赋值的。结构体赋值的原则是将整个结构体所指向的内存直接拷贝。

> 当然，这里可以直接赋值其实取决与我结构体中数组的定义方式。

{% hint style="info" %}
我举一个简单的例子：

```c
typedef struct {
    char name[12];
    int age;
} School;
```

定义两个上述类型的结构体变量：

```c
School p1 = {"nju", 120}, p2;
```

然后我们将p1赋值给p2:

```c
p2 = p1;
```

其实上面的赋值操作就相当于：

```c
#include <string.h>
memcpy(&p2, &p1, sizeof(School));
// 此处的sizeof(School) = 16
```

不过需要注意的是，如果我这样定义结构体：

```c
typedef struct {
    char* name;
    int age;
} School;
```

直接赋值的时候就不是拷贝字符串，而只是拷贝字符指针了。因为，此时：

```c
sizeof(School) = 8; // 而不是12了
```


{% endhint %}

因为我们需要保证所有的用户用户名不同，所以我们需要写一个`searchUserName`函数来查找用户名，只有找不到的时候，才可以为这个新的用户生成一个独特的`ID`并把新用户放到我们的数组中去。

因为这个`searchUserName`其实只在`addUser`函数中用到了，相当于添加新用户的时候检查是否用户已存在的一个帮助函数，因此使用`static`关键字将其作用域限制在本模块内，并且不在头文件中给处该函数的声明。

{% hint style="warning" %}
此处，你需要理解模块抽象、封装的过程和手法，在后面的教程中相似的内容我将不再重复。
{% endhint %}

#### 删除给定ID的用户

```c
// user.c
#include "user/user.h"

#include <string.h>

/**
 * @brief 查找给定ID的用户
 * 
 * @param id 需要查找的用户ID
 * @return int 成功返回下标，失败返回-1
 */
static int searchUserID(const char* id) {
    for (int i = 0; i < totalUser; i++)
        if (strcmp(users[i].id, id) == 0) return i;
    return -1;
}

int deleteUser(const char* id, const char* who) {
    if (who) return 0;
    int idx = searchUserID(id);
    if (idx == -1) return 0;
    for (int j = idx; j < totalUser - 1; j++) users[j] = users[j+1];
    totalUser--;
    return 1;
}
```

> `who`中存放的是行为主体的ID，只有管理员有权限删除用户（管理员ID为`NULL`）。

#### 打印所有用户的信息

```c
// user.c
#include "user/user.h"
#include "tools/info.h"

#include <stdio.h>

static const char* header = "|ID         |Name       |Contact    |Address    |Balance    |";
static const char* divide = "+-----------+-----------+-----------+-----------+-----------+";

/**
 * @brief 打印单个用户作为表格的一行
 * 
 * @param i 用户下标
 */
static void printUser(int i) {
    printf("|%-10s |%-10s |%-10s |%-10s |%-10.1f |\n", users[i].id, users[i].name, \
users[i].contact, users[i].address, users[i].balance);
}

void printUsers() {
    print_header
    for (int i = 0; i < totalUser; i++) {
        printUser(i);
        print_divide
    }
}
```

这里我们将表头`header`和表格分割线`divide`抽象出来，作为两个模块局部变量，在`Good`和`Order`模块中也有相同名称的局部变量。

这样设计是为了简化代码，提高通用性，因为表头和分割线会被使用很多次（这个模块不明显，因为只有管理员有权限查看所有的用户，不过在Good模块的时候，买家、卖家和管理员查看到的商品列表是不一样的，也就需要很多不同的print函数，所以会重复的要用到表头和分隔符）。

```c
// tools/info.h

// helper macro for print
#define print_header \
    printf("%s\n", divide);\
    printf("%s\n", header);\
    printf("%s\n", divide);

#define print_divide \
    printf("%s\n", divide);
```

除此之外，我还将打印表头和打印分隔符的操作封装成了两个宏`print_header`和`print_divide`，放在`info.h`头文件中。在所有的数据结构模块中都可以使用这两个宏。

因为`header`和`divide`都是`static`作用域，所以在不同的数据结构模块里面使用这两个宏，所指向的`header`和`divide`都是模块内自己定义的那两个，所以这两个宏只需要定义一遍就可以供三个数据结构模块多次使用，大大减少了不必要的重复性的代码。

{% hint style="warning" %}
这个地方最好能够驻足思考一下这种处理方式———利用预处理阶段宏展开的性质精简代码、利用static文件作用域的特性保持宏对于多个模块的实用性与泛化特性。

这个地方只是一个print的优化处理，在写interface模块的时候你会发现这样的处理能够精简特别多不必要的重复代码书写。

所以在继续之前还请稍微理解一下这样的处理方式。
{% endhint %}

{% hint style="warning" %}
在这个部分还用到了printf函数的各种格式化输出的功能，如果对于模式串含义还不清晰的话可以寻找相关教程复习一下。

参考教程： [https://www.runoob.com/cprogramming/c-function-printf.html](https://www.runoob.com/cprogramming/c-function-printf.html)

之后的内容中笔者会默认各位清楚了printf模式串中各种规定符的含义。
{% endhint %}

#### 检查用户名和密码是否匹配

```c
// user.c
#include <string.h>

int checkPass(const char* name, const char* passwd, int* idx) {
    *idx = searchUserName(name);
    if (*idx == -1) return 0;
    return strcmp(users[*idx].passwd, passwd) == 0;
}
```

这里除了返回密码匹配是否成功之外，还需要返回和哪一个用户匹配成功了。

{% hint style="info" %}
一个函数就只能有一个返回值，所以我们通过指针参数来返回到底和哪一个用户匹配成功了。

当我们要反馈不止一个值的时候，除了函数返回值以外，还可以诉诸于指针，通过传址调用的方式利用函数的副作用返回数据。
{% endhint %}

#### 用户充值

```c
int userTopUp(const char* id, double m) {
    int idx = searchUserID(id);
    if (idx == -1) return 0;
    users[idx].balance += m;
    return 1;
}
```

{% hint style="success" %}
至此，我们完成了用户数据结构模块的设计。提供了一些很方便使用的外部接口，等到我们之后写交互界面的时候就可以很方便的使用这些接口了。再接再厉，我们下面写一下商品模块。
{% endhint %}
