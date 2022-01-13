---
description: WinterCode Project for C-Beginners.
---

# 引入

**Email： 201220014@smail.nju.edu.cn**

## 概况与环境&#x20;

大部分的应用型程序其实从大框架上来讲就是一个信息管理系统，比如淘宝，微信，B站，微博等等。信息管理系统也是大部分开发者学习阶段上手的第一个项目。Whale Market是一个基于命令行的简易交易平台。

参考版本：[https://github.com/201220014/WhaleMarket](https://github.com/201220014/WhaleMarket)

设计参考南京大学程设基础实验课程项目一所提出的需求。

本教程会手把手详细叙述开发的全过程，并且会尽量在代码中覆盖程序设计基础课程的大部分内容，特别是老师轻描淡写带过的一些细节内容，以保证本项目的教学性。

在线开发平台地址：[http://39.101.72.84:8080/](http://39.101.72.84:8080)

> 游客用户名：anonymous
>
> 游客密码：123456
>
> 这个账户只拥有查看权限，并没有修改权限。

WebSSH服务地址：[http://39.101.72.84:8888/](http://39.101.72.84:8888)

目前只支持部分内部人员使用在线平台，当然，代码是开源的，您也可以搭建自己的离线环境，需要的环境很简单：`editor + linux + gcc + make`。其中，编辑器以及Linux发行版可以根据自己的喜好选择。

{% hint style="info" %}
对于刚接触Linux的同学，Ubuntu桌面版Linux和VSCode编辑器会比较友好一些。

Ubuntu： [https://ubuntu.com/](https://ubuntu.com)

VSCode： [https://code.visualstudio.com/](https://code.visualstudio.com)

Linux文件管理基本命令： [https://www.runoob.com/linux/linux-file-content-manage.html](https://www.runoob.com/linux/linux-file-content-manage.html)
{% endhint %}

## 需求汇总

再这个项目中，我们需要实现如下的基本需求：

* 用户功能：
  * 登陆、注册、注销
* 买家功能
  * 查看、搜索商品
  * 购买商品
* 卖家功能
  * 商品的发布、下架
  * 查看已发布商品
  * 修改商品信息
* 查看历史订单
* 个人信息查看、修改
* 管理员功能
  * 登陆、注销
  * 查看、搜索、下架商品
  * 查看所有订单
  * 查看、删除用户
* 系统功能
  * 程序结束后再次打开程序，原本的数据不能丢失

{% hint style="info" %}
注意：不同的主体增删查改的权限是不一样的。
{% endhint %}

## 设计思路

针对涉及到的各种主体设计不同的数据类型，然后选择一种合适的数据结构在内存中存储数据，使用文件读写的方式来与外存交互，以保证程序结束后再次运行数据不丢失。



## 文件架构


