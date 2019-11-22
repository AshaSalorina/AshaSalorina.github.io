---
![]()layout: post
title:  "在Unity3d中使用shader graph#1"
date:   2019-11-22 14:20:00 +0800
categories: unity3d shader_graph
img: https://i.loli.net/2019/07/11/5d26582f5d69d58772.png  
---
# 在Unity3d中使用shader graph#1

在Unity3d 2018之后提供的shader graph，这几天终于在工作中用起来了。

趁着现在刚做完部分效果，在博客中进行一下记录。

Unity版本：2019.1.7f1

## 1.安装依赖

Shader graph需要通过Unity的包管理器（Window-Package Manager）安装  

依赖有两个：  

| 包名               | 当前版本（适用于Unity2019.1.7f1） |
| ------------------ | --------------------------------- |
| **Lightweight RP** | 5.7.2                             |
| **Shader Graph**   | 5.7.2                             |

## 2.为shader graph设置可编程渲染管线的资源

1）打开项目设定，找到graphics栏目（Edit-Project Setting-Graphics）  

2）找到Scriptable Render Pipeline Settings项（本项此后定义为$a）  

3）在Project视图中任意位置新建一个**可编程渲染管线**（Create-Rendering-LightweightRenderPipeline）  

4）将新建的资源拖拽到($a)的位置  

## 3.开始编写第一个shader graph

同样在Project视图中任意位置（推荐Resources/Shader Graph/）创建一个任意后缀为Graph的shader(Create-Shader-XXX Graph)  

这里推荐2d使用Unlit Graph，3d使用PBR Graph  

本例使用Unlit Graph

----
