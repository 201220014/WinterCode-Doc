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

这里，我们引入了一个头文件config.h，这个头文件是储存一些全局设定的，这里我们约定本项目中所有的字符串长度均不超过`MAX_LEN`，我们将其定义在`config.h`中。

```c
// config.h
#define MAX_LEN     64
```

随着程序的不断丰富，我们可能会需要一些其他的全局设定或者约定，我们都可以放在`config.h`中。这样一来，如果之后我们要更改这些约定，只需要修改`config.h`头文件即可。

## 选择数据结构

我选用了数组结构，因此我们需要约定用户的总数不超过`MAX_USER`，并且在config.h中定义这个宏。

```c
// config.h
#define MAX_USER    128
```

我们在user.c中定义这个数据结构，同时定义用户的初始总数为0：

```c
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
 * @brief 用户信息复制，将src用户的信息复制给dest用户
 * 
 * @param dest 目标用户指针
 * @param src 源用户指针
 */
void userCopy(User* dest, const User* src);

/**
 * @brief 获取用户对象
 * 
 * @param idex 用户在的数组下标
 * @return User* 指向对应用户的指针
 */
User* getUser(int idex);

/**
 * @brief 打印的详细用户信息
 * 
 * @param i 用户的数组下标
 */
void userInfo(int i);

/**
 * @brief 查找给定ID的用户
 * 
 * @param id user id to be searched
 * @return int -1 if failed else index in users
 */
int searchUserID(const char* id);

/**
 * @brief search user with name
 * 
 * @param name username to be searched
 * @return int -1 if failed else index in users
 */
int searchUserName(const char* name);

/**
 * @brief add user u to users
 * 
 * @param u user to be added
 * @return int 0 if failed else 1
 */
int addUser(User* u);

/**
 * @brief delete user u with id id
 * 
 * @param id user id to be deleted
 * @param who who does the deletion
 * @return int 0 if failed else 1
 */
int deleteUser(const char* id, const char* who);

/**
 * @brief print all users' infomation
 * 
 */
void printUsers();

/**
 * @brief check if name and passwd are matched
 * 
 * @param name username
 * @param passwd password
 * @param idx index of user if exists else -1
 * @return int 1 if matched else 0
 */
int checkPass(const char* name, const char* passwd, int* idx);

/**
 * @brief top up money 
 * 
 * @param id user id
 * @param m money
 * @return int 0 if failed else 1
 */
int userTopUp(const char* id, double m);
```
