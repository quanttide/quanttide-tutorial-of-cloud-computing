---
title: 标准项目转换成函数计算项目
author: 
coauthors:
  - 
reviews: 
  - 
created_date: 
updated_date: 
goals: 
  intro: 入门函数计算
outlines:
  intro: 把一个Python普通模块改造成函数计算并部署的过程。
  details:
    - 在`main`函数改为`main(event, context)`格式
    - 通过云函数控制台上传。
    - 运行云函数测试。
expections:
  intro: 学习者发现它真的很简单，而且可以干很多事情，被激发了继续学习的兴趣。
references:
  - link: https://quanttide.coding.net/p/qtservices-xieyi-douyin/wiki/5
    descrpition: 把快速入门部分留下，其余的移到后面的部分。增加一些代码案例。
---

# 标准项目转换成函数计算项目

<!--
Metadata的title。
可以考虑不要，不一定需要渲染。
-->

## 概述

<!--
Metadata里的简介以说人话的方式表述。Metadata的数据不会在文章中渲染，所以在开头以说人话的方式说一遍。
-->

本讲的教学目标是入门函数计算。

本讲的教学思路是讲述把一个Python普通模块改造成函数计算并部署的过程。

1. 在`main`函数改为`main(event, context)`格式
2. 通过云函数控制台上传。
3. 运行云函数测试。

本讲的预期效果是让学习者发现它真的很简单，而且可以干很多事情，被激发了继续学习的兴趣。

# 一、函数计算简介

这里比较建议给读者一个思考的出发点，建议是一个具体的疑问。


# 二、创建一个云函数

打开云函数服务控制台 <https://console.cloud.tencent.com/scf/index?rid=16>

![图片](/api/project/8742169/files/26152127/imagePreview)

在左侧函数服务中可以看到你拥有的函数列表，也能够创建一个新函数。

如果你是新手的话，我们建议选择自定义创建。

观察函数配置，根据需要修改配置，没有需要的话可以保持默认配置。

函数代码部分，将自己的代码复制粘贴到控制台中，

这里便是云函数计算与普通函数的不同之处：我们需要将自己的代码改成云函数需要的样子，具体操作为：

将主函数添加`event、context`参数，给这两个参数赋上你需要的值。

根据你的函数做出修改。

举个例子：
```python
# 创建一个函数

def Print():
    print("Hello World!")


def main():
    Print()


if __name__ == "__main__":
    main()
```

```python
# 改造成云函数需要的样子

def Print():
    print("Hello World!")


def main(event,context):
    Print()


if __name__ == "__main__":
    event = {}
    context = {}
    main(event, context)

```

这里`main`函数改为`main(event,context)`格式，在最后将参数赋值（空值）并调用

点击完成。

>**注意：执行方法中函数入口的文件名和文件夹中将执行的python文件名保持一致**

没有问题的话，创建成功，跳转到云函数界面。


# 四、测试云函数

跳转到新页面，观察页面。

函数配置部分你可以修改你的函数配置， 函数代码部分你可以测试你的函数并查看返回结果以及日志。

点击函数框面下方的测试便可以测试函数，查看返回结果。

![图片](/api/project/8742169/files/26098906/imagePreview)

根据你的函数返回的结果以及日志修改代码让其顺利运行。

## 参考资料

<!--
在这里以相对规范的格式引用参考的文档等资料。
-->
