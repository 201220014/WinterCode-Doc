---
description: 在正式开始写代码之前，我们先了解一下项目的构建方式。
---

# 项目是怎样构建的

## C程序是怎样生成的

```c
// hello.c
#include <stdio.h>

int main() {
    printf("Hello, WinterCode!\n");
    return 0;
}
```

c程序，或者说c家族程序从源代码到可执行程序的过程可以用下图表示：

![](.gitbook/assets/pic02.png)

经过了编译预处理、编译、汇编、链接四个阶段。

在linux下使用gcc构建c程序的过程（以hello.c为例）可以用如下命令表示：

```bash
gcc -c hello.c -o hello.o # 编译
gcc hello.o -o hello # 链接
```

当然，如果是单模块的话也可以合并为一个命令：

```bash
gcc hello.c -o hello
```

之后，我们只需要运行二进制文件hello就可以运行我们的程序了。

```bash
./hello
Hello, WinterCode!
```

## C项目是怎样搭建的

我们以WhaleMarket的`Makefile`为例来看一下C工程是如何搭建的。

```makefile
.PHONY: clean # 伪目标

# 自定义环境变量
CC = gcc # 指定编译器

CFLAGS = -I include # 指定头文件目录
CFILES = $(shell find src -name "*.c") # 搜索所有的源文件
OBJS = $(CFILES:.c=.o) # 所有的目标文件
TARGET = main # 最终生成目标
DATA = src/data/*.txt # 搜索所有的数据文件

RM = -rm -f # 删除方式

all: $(TARGET)
	git commit -a -m "> make"

# 项目构建方式
$(TARGET): $(OBJS)
	$(CC) -o $(TARGET) $(OBJS)

%o : %c
	$(CC) -c $(CFLAGS) $< -o $@

clean:
	$(RM) $(TARGET) $(OBJS) $(DATA)
	git commit -a -m "> make clean"
```

其中我们关注如下内容：

```makefile
# 项目构建方式
$(TARGET): $(OBJS)
	$(CC) -o $(TARGET) $(OBJS)

%o : %c
	$(CC) -c $(CFLAGS) $< -o $@
```

