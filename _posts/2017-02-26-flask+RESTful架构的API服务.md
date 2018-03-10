---
layout: post
title: Flask+RESTful架构的API服务
categories: Unix
description: Flask
keywords: Flask, RESTful
---

### **Flask简介**
- flask框架是基于python语言的轻量级web服务器框架，搭建简单，部署轻便，而且能够满足大部分的业务场景，诸多优点使得flask web框架受到许多开发者的青睐。笔者初识flask时，就被flask的"微"(micro)给惊艳到了。在flask官网中，如此介绍道："微框架的'微'意味着保持核心简单而易于扩展"，默认情况下，Flask不包含数据库抽象层，表单验证(此功能可以借助其他三方python的web库进行友好衔接、实现)。同时，官方中文文档中有很好的入门例子[传送门]<http://docs.jinkan.org/docs/flask/foreword.html#id2> 如下，是一个简单的Flask服务器样例
```python
form flask import Flask

app = Flask(__name__)

@app.route('/')
def welcome():
    return "Welcome to studement management system!"

app.run(host="192.168.1.1", post=1234, debug=True)
```

- 笔者之前在开发一个类似知乎论坛的短文本语料分类系统，需要将项目部署到服务器上，并提供API服务调用。短文本分类系统是基于时下较为热门的谷歌开源深度学习框架tensorflow实现的，后续文本通过请求服务器API返回类别标签，因此预测相关运算由服务器承担，即客户端生产，服务器消费。目前是借助了Flask+RESTful架构搭建了API服务。

- **什么是RESTful？** REST的全称：Representational State Transfer，即表征状态转移，其实这里隐含省略了主语----Resources(资源)

1. **资源(Resources)**，就是网络上的一个实体，可以指一个图片，文本，或者服务。你可以用一个URI指向它。每种资源对应一个特定的URI，则可以通过访问URI来获取对应的资源。生活中提到的上网，换句话说就是与网络上的资源进行一系列的互动。

2. **表征(Representation)**，如何把资源表现出来，即是表征的意义。如一段文本是txt、html还是json，图片是jpg还是png。以http为例，则指Accept和Content-type中的内容，指明了资源的类型。

3. **转态转移(State Transfer)**，访问网站，其实就是客户端和服务端交互的过程，客户端操作服务端，必须通过某种手段或协议让服务端发生变化，如http协议的几种方法：GET用来获取资源，POST用来新建资源，PUT用来更新资源，DELETE用来删除资源。

**《待续》**
1. 究竟什么是RESTful呢？
2. 使用URI表示每一个资源
3. 确定每一个资源的表现形式
4. 使用4个方法操作这些资源
