# 目录
1. 为什么使用Dockerfile？
2. Dockerfile基本语法

## 为什么使用Dockerfile？
在前面我们学习过使用命令行生成镜像，我们可以进入容器内部下载第三方包然后再把这个容器打包成镜像（commit）。
但是有没有可能直接用一种脚本文件就可以生成我们想要的镜像呢？这就是Dockerfile。

Dockerfile使得我们不用实际去操作容器生成镜像，而是把这些操作写在一个Dockerfile文件里面，
然后使用命令docker build来生成镜像。

## Dockerfile基本语法
### 一个简单的例子
```dockerfile
FROM python:3.7.9
LABEL maintainer = 'zouzhenhao@quanttide.com'
LABEL version = '1.0'
LABEL description = 'Just for tutorial'
ADD requirements.txt .
RUN pip install -r requirements.txt -i https://pypi.tuna.tsinghua.edu.cn/simple
WORKDIR /app
CMD ["/bin/bash"]
```
我们可以把这个文件命名为my_python.Dockerfile。然后用命令行输入：
```dockerfile
docker build --rm -t my_python:1.0 .
```
用Dockerfile生成镜像的标准命令为：
```dockerfile
docker build [OPTIONS] PATH | URL | -
```
常用option为：
```dockerfile
-t: 镜像的名字及标签，通常 name:tag 或者 name 格式
--rm :设置镜像成功后删除中间容器
-f :指定要使用的Dockerfile路径
```
因此，我的命令意思是生成一个名为my_python:1.0的镜像，同时删除期间产生的所有容器，
Dockerfile文件的地址在当前目录。

**注：.代表在当前目录，后面讲到WORKDIR也会提到。**

### FROM
来学习第一个命令FROM，字面意思就是这个镜像从什么镜像基础上做的。可以类比为：
```dockerfile
docker pull python:3.7.9
```
我们尽量都在官方给的镜像模板上面做一些修改。毕竟自己从头开始做镜像很麻烦。
注意，FROM需要在Dockerfile的第一行，之后的所有操作都是基于这个基础镜像展开的。

### LABEL
LABEL可以用来表明这个Image的作者，版本，描述等信息。不是必须的。

### WORKDIR
WORKDIR用来说明工作目录在哪。生成容器后就会自动进入指定的工作目录。
建议自己建一个工作目录，这样可以和环境文件隔离。注意上面/app，如果系统没有这个路径，支持自动创建app目录。

### ADD/COPY
用来将本地工作目录放到容器中。比如本地工作目录中有requirements.txt，我们希望把它放到镜像中，
便于我们生成。
```dockerfile
ADD requirements.txt .
```
意为把本地的requirements.txt放入镜像的当前工作目录中。这样我们pip install的时候就能找到这个requirements文件了。

### ENV
指令ENV就是设置一个环境变量。

### RUN
RUN是用来说明在你生成目的镜像之前，需要做什么额外工作。一般是用来下载一些第三方包之类的。

注意：每使用一个run命令则会叠加一层镜像，我们应尽可能将多个run命令写成一个run命令。

```dockerfile
RUN apt-get update
RUN apt-get install -y curl nginx # 这是多行写法不提倡

RUN apt-get update && apt-get install -y curl nginx # 提倡单行写法
```

### EXPOSE
EXPOSE是用来暴露容器的端口的。一般来说，我们需要暴露一些端口。比如对一个有Apache网络服务器镜像来说，
需要暴露端口80，对于一个含有Mongodb的镜像来说要暴露端口27017。

### CMD
CMD命令用来跑镜像里面的软件的。基本格式为：
```dockerfile
CMD ["executable", "param1", "param2"…]
```
比如我们想跑一个python文件，就可以是：
```dockerfile
CMD ["python","main.py"]
```
对于新手来说，更多情况之下是跑一个交互式shell，比如bash，python之类的，命令可以为：
```dockerfile
CMD ["/bin/bash"]
CMD ["python"]
```
**注意：CMD命令只能出现一次，如果出现多次，则只会执行最后一个CMD命令。**

下面两个命令是等价的：
```pycon
docker run -it 镜像名 /bin/bash
```
和
```pycon
CMD ["bin/bash"] # 写在dockerfile中
docker run -it 镜像名
```