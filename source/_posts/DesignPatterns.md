---
title: 设计模式读书笔记
katex: true
date: 2021-06-19 16:54:08
tags:   
    - 设计模式
keywords:
categories:
    - 读书笔记
description: 记录阅读《设计模式：可复用面向对象软件的基础》的一些感想
---

## 第一章
{% note 'fas fa-text-height' %}
根据模式的性质，将其划分为三种类型：创建型（creational）、结构性（structural）、行为型（behavioral）
一般而言，一个模式有四个基本要素：模式名、问题、解决方案、效果
{% endnote %}

* 最常用的设计模式：
    - Abstract Factory(抽象工厂模式)：提供一个创建一系列相关或相互依赖对象的接口，而无需指定它们具体的类
    - Adapter(适配器模式)：将一个类的接口转换成客户希望的另一个接口。Adapter模式使得原本由于接口不兼容而不能一起工作的那些类可以一起工作
    - Composite(组合模式)：将对象组合成树形结构以表示“部分-整体”的层次结构。Composite使得客户对单个对象和组合对象的使用具有一致性
    - Decorator(装饰器模式)：动态地给一个对象添加一些额外的职责。就扩展功能而言，Decorator模式比生成子类方式更为灵活
    - Factory Method(工厂方法)：定义一个用于创建对象的接口，让子类决定将哪一个类实例化。Factory Method使一个类的实例化延迟到其子类
    - Observer(观察者模式)：定义对象间的一种一对多的依赖关系，以便当一个对象的状态发生改变时，所有依赖它的对象都得到通知并自动刷新
    - Strategy(策略模式)：定义一些系列的算法，把它们一个个封装起来，并且使它们可相互替换。本模式使得算法的变化可独立于使用它的客户
    - Template Method(模板方法)：定义一个操作中的算法的骨架，而将一些步骤延迟到子类中。Template Method使得子类不改变一个算法的结构即可重新定义该算法的某些特定步骤

![设计模式空间](https://cdn.ppunknown.cn/images/posts/28a324389441b5b71d5ad87b45c68cb.png)

{% note 'fas fa-text-height' %}
一些名词的解释
    动态绑定：发送给对象的请求和它的相应操作在运行时的连接称为{% label 动态绑定 blue %}
    多态： 动态绑定允许运行时彼此替换有相同接口的对象。这种可替换性就称为{% label 多态 blue %}
{% endnote %}

{% note 'fas fa-text-height' %}
对象的类（class）与对象的类型（type）之间的差别：
    对象的类定义了对象是怎样实现的，同时也定义了对象的内部状态和操作的实现。但是对象的类型只与它的接口有关，接口即对象能相应的请求的集合。一个对象可以有多个类型，不同类的对象可以有相同的类型
{% endnote %}

![设计模式所支持的设计的可变方面](https://cdn.ppunknown.cn/images/posts/L3XR%5DDY14%7D0HDI7ZKH89%609B.png)