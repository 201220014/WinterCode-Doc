---
description: 让我们为自己的项目编写一些实用的工具吧！
cover: >-
  https://images.unsplash.com/photo-1497215728101-856f4ea42174?crop=entropy&cs=srgb&fm=jpg&ixid=MnwxOTcwMjR8MXwxfHNlYXJjaHw4fHx0b29sc3xlbnwwfHx8fDE2NDIyNzE4NDY&ixlib=rb-1.2.1&q=85
coverY: -267.817575083426
---

# 🛠 Tools模块

有很多内容会在整个项目过程中像工具一样重复使用，我们可以把这些内容抽离出来，单独作为一个门类，放在tools文件夹下。比如说框架代码中的color模块就是这样的一种角色。

存放Banner和Invalid信息的hint模块也是这样的，不过我们其实可以将各种各样的提示信息全部都放在hint模块里面，这样在写其他模块的时候就不用再重复写了。此外，如果我们没有想要添加某种提示信息，也只需要在hint模块里面添加一遍就可以全局（只要包含`hint.h`头文件）使用了。

还有ID的生成和日期的获取也可以作为info模块放在tools文件夹里面，因为这两个信息的生成与获取也是很常用的。

{% hint style="success" %}
现在，你应该已经理解了tools文件夹下到底是放置哪些模块的，下面我们来实现这两个模块。
{% endhint %}

