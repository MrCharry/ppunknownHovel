---
title: 通过邮政编码获取城市街道信息
date: 2021-05-31 07:49:05
tags: 
	- 数据集
	- 邮编
categories: 方案调研
katex: true
keywords: zipcode 
description: 探究通过邮政编码获取城市信息的方法
---
# Cities by ZipCode 功能调研
## 可选方案
- [GeoName](https://www.geonames.org)
- [Zippopotam](http://api.zippopotam.us)
- 自建数据源

## 方案比较
方案 | 数据来源 | 调用方式 | 返回数据格式 | 调用限制 | 数据及时性 | 服务稳定性 | 实现复杂度
:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:
GeoName |geonames.org | HTTP | XML/JSON | 20000(c/d) | 高 | 高 | 低
Zippopotam | geonames.org | HTTP | JSON | ∞ | 中 | 较高 | 低
自建数据库 | geonames.org | 本地调用 | JSON | ∞ | 低 | 最高 | 高
**c/d: credit/day**

	1. 目前搜集到的方案绝大部分的数据源都来自geonames.org

	2. GeoName提供Web API提供调用，但限制次数为20000(c/d)，调用不同的接口耗费不同的credit，直接使用GeoName提供的服务可以获得及时更新的数据源，缺点是有调用频率限制（提供付费购买额度）。同时，GeoName也提供离线数据库，分为免费版和premium版，premium版需付费，但是数据一致性优于免费版

	3. Zippopotam提供的服务与GeoName类似，缺点没有自己的数据源，数据源也是使用的GeoName的数据源，是基于GeoName的二次开发产品。优点是其提供的接口没有调用次数限制

	4. 自己基于GeoName的离线数据库构建自己的数据源。这样能够获得最大的灵活性和最高的可用性，缺点是无法及时获取最新的数据

## 拟选择方案
	计划使用基于GeoNames的数据源自建数据库的方式来实现填写ZipCode自动填写表单的需求。根据前端传递的国家和邮政编码参数查询数据库返回相应的州、城市、街道等消息（用户可以自行修改）。数据准确性通过创建一个定时同步GeoNames数据源的计划任务来保证

- 数据库选择
	1. MySQL：基于现有架构，无需额外引入新的数据库系统，实现简单，但是同步数据源较复杂
	2. MongoDB：需要新引入MongoDB，非常适合JSON数据查询，效率高，数据同步方便

## 初步方案设计
- 流程图
![流程图](http://showdoc.ppunknown.cn/server/index.php?s=/api/attachment/visitFile/sign/02f7688935d02719920a277e797390ae)

## 参考资料
- [GeoNames Web Service](https://www.geonames.org/export/ws-overview.html)： GeoNames 可提供的 API 服务
- [GeoNames Web Service Call Comsumption](https://www.geonames.org/export/credits.html)：调用单次 GeoNames API 需消耗的 credit 对比表
- [GeoNames Postal Code files](http://download.geonames.org/export/zip/)：GeoNames 邮政编码文件数据结构描述及下载地址
- [Zippopotam](http://api.zippopotam.us)：Zippopotam 可提供的API服务
- [Postal code vs Zip code](https://keydifferences.com/difference-between-zip-code-and-postal-code.html)：Postal code 与 Zip code 的异同
- [GeoNames DataSource](http://www.geonames.org/data-sources.html)：GeoNames 数据源列表