---
title: 管理函数计算依赖
outlines:
  details:
    - 普通Python模块在Win或者Mac打包没有问题，带操作系统的模块会出问题。原因是Python包有一些操作系统依赖部分，最好的解决方案是用CI，装虚拟机、远程服务器、用容器等方案都很麻烦。（第一次遇到这个问题排查了两三个星期，详见田锃的复盘。）
    - 反复使用同一个或者一类、或者一堆云函数共同依赖一个或者一些，可以用层管理。
references:
  - link: https://quanttide.coding.net/p/project-reviews/d/project-reviews-on-tech/git/tree/master/cloud_computing/cloudbase/2021-03-06_start_function_computing.md
  - description: 田锃的复盘
---

# 管理函数计算依赖
