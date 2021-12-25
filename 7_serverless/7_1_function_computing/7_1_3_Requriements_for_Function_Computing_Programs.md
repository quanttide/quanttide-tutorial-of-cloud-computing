# 云函数的依赖

本讲的教学目标是：
	

 - 认识依赖和云函数部署依赖的方式
 - 打包上传云函数依赖
 - 云函数的层管理


## 一、依赖介绍

python本身做为一门解释性语言，说它功能强大，是因为它有着丰富的模块或称之为列表依赖(包)，一些热衷于开源的朋友开发了应用于不同领域使用的第三方模块，一起构成了python强大功能的生态。
为了直观体会依赖的作用，你可以想象一下使用python求开平方的函数代码。

    sqrt = math.sqrt(x)
而如果你想要自己完成，是需要一定时间并投入一定精力的（具体的开平方算法读者可以自行了解）。使用python依赖的好处，正是省去这部分已经被他人完成的重复劳动，通过简单的调用Api或者其他途径简单实现想要的功能。如果你对于很多依赖都并不熟悉，这并不会很大程度上影响你的云函数体验，你完全可以在需要使用时再去学习你要使用的某一特定库。

>  本章将以常用数据分析库numpy为用例，介绍云端依赖部署和使用过程。



## 二、依赖部署的方法

> 由于云函数的不断完善，许多库已经被内置在云函数计算中，读者可以前往https://cloud.tencent.com/document/product/583/55592 自行查看。


### 1、本地打包依赖上传
在你完成云函数的其他准备后，你可以使用pip文件将依赖库安装到同级文件夹，然后一起打包上云。

    cd <path>
    pip install numpy -t 

这里建议使用zip压缩包打包上云，即先进行亚索然后在云函数部署页面选择本地上传zip包。依赖便会与函数代码一同部署在云函数服务中。
>
这里再介绍另一种打包方式，使用requirements配合脚本进行打包。首先我们先来了解一下requirements文件。（假设读者已经安装了numpy依赖库）
- 使用pip脚本导出requirements文件

		pip freeze > requirements.txt
- 然后我们打开requirements文件，可以看到

	    numpy==1.21.4
当然，除了你已经安装的依赖库以外，你可能还会看到其他你没有见过的依赖，这是正常的，因为依赖本身可能也会需要依赖。
requirements的语法非常简单，前面是依赖库的名字，后面是依赖库的版本号。

而安装requirements时，可以使用pip脚本安装，这样就可以安装与本地环境相同的依赖环境。
`pip install -r requirements.txt`

你可以在多个文件夹中放置被其中python文件需要的requirements，然后使用python脚本打包。

    import os
    import sys
    
    import subprocess
    
      
    
    for directory in os.listdir():
	    if os.path.isdir(directory) and  'requirements.txt'  in os.listdir(directory):
    		    subprocess.check_call(['pip3', 'install', '-r', os.path.join(directory, 'requirements.txt'), '-t', directory])

> 
### 2、使用云端ide控制台终端pip安装
如果你已经提前部署了函数代码，但是发现缺少相关依赖，可以重新打包进行上传，但是非常的繁琐。
事实上，你可以直接在云端ide的控制台调用相关命令执行依赖库的安装。

- 首先在云函数服务页面选择需要安装依赖的云函数，进入函数管理-> 函数代码-> 终端
- 等待加载完成后运行命令

		pip install numpy


> 至此，基础的依赖上云方法已经介绍完了，但是云函数本身也存在着各种兼容问题，可能ide会在代码开头的import中显示错误，属于正常现象，请通过测试来尝试是否有依赖问题。
> 如果你尝试了各种方法依然无法部署，这可能与系统和依赖的兼容性有关系。之后的篇章中会介绍其他的部署方法，如虚拟机安装依赖，ci流水线依赖安装等等 


