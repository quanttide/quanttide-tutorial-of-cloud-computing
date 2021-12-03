---
title:
author: 张果
coauthors:
  - 
reviewers:
  - 
goals: 
  intro: 理解函数计算的基本单元——事件函数和触发器。
outlines:
  intro: 
  details:
    - 事件函数没啥要讲的，重点是和触发器结合。
    - 触发器以HTTP为重点，其他的提一下（因为不熟悉）。
---

# 函数计算

函数计算引擎的工作原理是这样的：

1. 来自其他应用的**触发器**触发函数运行，并给函数传递必要的参数；
2. 函数的主函数是一个**事件函数**，接收参数并转换成函数内可以理解的数据格式；
3. 像其他程序一样运行函数内部的Native代码。

我们可以看到，触发器和事件函数是函数计算引擎的关键点，触发器发送消息，事件函数接收和解析消息，用以实现函数计算内外部的通信。最常见的触发器就是HTTP代理，触发器把HTTP报文解析并根据事件函数的传参规范传给事件函数，事件函数根据函数计算的标准解析并传给Native。通过这样的机制，我们可以很轻松地根据标准实现一个事件函数，然后使用云服务提供的函数计算框架部署成一个API接口。由于事件函数是通用的，理论上如果有合适的触发器和编排触发规则的工具，我们可以用它来做一些流程相对固定的应用和流水线，包括Web服务、爬虫、数据分析、机器学习等等。

### 事件函数

各家云厂商的事件函数规范，都直接follow了最早提出函数计算的AWS Lambda。我们以Python为例解析一个事件函数的规则。

Python的事件函数代码包必须包括一个执行入口（比如`index.py`），其中必须有一个事件函数的规范格式实现（比如`main`函数），具体格式如下：

```python
# index.py

def main(event, context):
    # do something
    return 'result'
```

其中，`event`参数的字面理解是“事件”，从名字可以知道这是触发器传递给事件函数的数据。（在事件驱动的编程中，所有的消息都是以“事件”为单位传递的。）如果是HTTP服务，则会把HTTP报文转换成`event`的参数。在Python中，`event`是一个`dict`格式。`context`参数字面意思是“上下文”，这里当然指的就是事件函数运行的上下文，也就是运行环境的信息。`return`返回值即是传递给触发器的返回值，根据触发器的加工即可变成其他程序需要的样子。比如，HTTP服务里会把返回值作为响应报文的body部分。

### 触发器

最常用的触发器是HTTP服务API，需要使用API网关触发器实现。API网关触发器根据预先配置的执行入口和事件函数（i.e. 本例的`index.main`），把请求报文转成事件给事件函数，接收返回值加工成响应报文。

各家云厂商除了支持HTTP服务，还会支持很多其他类型的触发器。比如对象存储触发器可以用来触发云函数做数据处理、图像和视频处理；消息队列触发器可以用来处理消息队列接收的消息；等等。

触发器为事件函数提供了和其他标准化应用的通信方式，让函数计算可以以灵活多样的方式接入其他应用，实现必要的功能。