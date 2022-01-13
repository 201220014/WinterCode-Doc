---
description: 生成各类ID和获取时间的内容封装成一个info模块。
---

# 📅 生成ID和获取时间

## 我们到哪儿了

```c
.---+- include -+- tools -+- color.h
    |                     +- hint.h
    |                     +- info.h <---
    | 
    +-   src   -+- tools -+- color.c
                |         +- hint.c
                |         +- info.c <---
                +- main.c
```

## 生成ID

在我们的项目中有三个主体需要生成唯一的ID——用户、商品、订单。

我们约定这三种ID分别以U(User), G(Good), O(Order)开头加上五位数字，从类型上讲是一个字符串。

### 接口设计

```c
// info.h

/**
 * @brief generate an ID
 * 
 * @param dest string to store the ID
 * @param type ID type
 */
void genID(char* dest, char type);
```

### 实现

```c
// info.c
#include <stdio.h>
#include <string.h>

static const char* filePath = "src/data/id.txt";

void genID(char* dest, char type) {
    int uid = 0, gid = 0, oid = 0; // user, good, order
    FILE* pf = fopen(filePath, "r");
    if (pf) {
        fscanf(pf, "%d%d%d", &uid, &gid, &oid);
        fclose(pf);
    }
    int id = 0;
    switch (type) {
        case 'U': id = uid++; break;
        case 'G': id = gid++; break;
        case 'O': id = oid++; break;
    }
    sprintf(dest, "%c%05d", type, id);
    pf = fopen(filePath, "w");
    fprintf(pf, "%d %d %d\n", uid, gid, oid);
    fclose(pf);
}
```





