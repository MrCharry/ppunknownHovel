---
title: MySQLWhereInOrder
katex: true
date: 2021-06-23 16:42:31
tags:
    - MySQL
keywords:
    - whereIn
    - 排序
categories:
    - 技术交流
description: 探究MySQL使用where in查询时返回结果的顺序的影响因素
---

* 假设我们有一个{% label players green %}运动员信息表，表中记录如下：

id | nickname | age | vocation
:-: | :-: | :-: | :-:
1 | Tom | 18 | teacher
2 | Cook | 15 | CEO
3 | Johm | 19 | gardener
4 | Smith | 21 | actor
5 | Sherry | 14 | artist
6 | Michael | 17 | singer
7 | Tony | 16 | barber
8 | Bryant | 20 | athlete

* 现在我们执行以下语句看看有什么效果：

```sql
SELECT * FROM `players` WHERE id IN (8, 5, 3);
```

* 可以看到，结果是：

id | nickname | age | vocation
:-: | :-: | :-: | :-:
3 | Johm | 19 | gardener
5 | Sherry | 14 | artist
8 | Bryant | 20 | athlete

* 怎么样？跟你想的是不是一样呀？接着，我们继续执行以下语句：

```sql
SELECT * FROM `players` WHERE age IN (19, 14, 20);
```

* 猜一下返回结果的顺序是什么？

| id | nickname | age | vocation |
| :-: | :-: | :-: | :-: |
| 5 | Sherry | 14 | artist |
| 3 | John | 19 | gardener |
| 8 | Bryant | 20 | athlete |

* 怎么样，猜对了吗？别急，再来看看下面一条语句：

```sql
SELECT * FROM `players` WHERE nickname IN ('Bryant', 'Sherry', 'John');
```
| id | nickname | age | vocation |
| :-: | :-: | :-: | :-: |
| 8 | Bryant | 20 | athlete |
| 3 | John | 19 | gardener |
| 5 | Sherry | 14 | artist |

* 看了这么多例子，怎么样，找到规律了吗？心里默念刚找到的规律，来看最后一条语句：

```sql
SELECT * FROM `players` WHERE vocation IN ('gardener', 'athlete', 'artist');
```

* 猜到返回结果集的顺序了吗？一起来揭晓答案吧！

| id | nickname | age | vocation |
| :-: | :-: | :-: | :-: |
| 3 | John | 19 | gardener |
| 5 | Sherry | 14 | artist |
| 8 | Bryant | 20 | athlete |

* 发现了吗？返回的结果竟然跟第一个例子是一样的。没猜到也没关系，带着疑惑，我们一起来解释一下得到以上这几种情况的原因吧！

{% note 'fas fa-text-height' %}
首先来看一看表结构：
```sql
CREATE TABLE `players` (
  `id` int(11) unsigned NOT NULL AUTO_INCREMENT COMMENT '自增id',
  `nickname` varchar(15) COLLATE utf8mb4_general_ci NOT NULL COMMENT '姓名',
  `age` int(3) unsigned NOT NULL COMMENT '年龄',
  `vocation` varchar(15) COLLATE utf8mb4_general_ci NOT NULL COMMENT '职业',
  PRIMARY KEY (`id`),
  KEY `index_age` (`age`),
  KEY `index_name` (`nickname`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_general_ci;
```
可以看到，字段 `id` 加了主键索引，`age`, `nickname` 都为普通索引，而 `vocation` 字段没有索引，现在是不是一下子明白了呢？下面分别对每个例子进行解释：
{% endnote %}

{% tabs 对比 %}
<!-- tab 例1 -->
对于 ```SELECT * FROM `players` WHERE id IN (8, 5, 3);```
由于查询条件 `id` 带有主键索引，MySQL返回数据时，如果查询的字段带有索引，会先根据索引排序，然后再返回。所以就不难理解为什么返回的顺序是 3,5,8 了
<!-- endtab -->

<!-- tab 例2 -->
对于 ```SELECT * FROM `players` WHERE age IN (19, 14, 20);```
查询条件是 `age`, 也是带有索引的字段，所以返回数据时，同样会先根据索引排序，最终返回结果为 14，19，20，也就是对应id 5, 3, 8
<!-- endtab -->

<!-- tab 例3 -->
对于 ```SELECT * FROM `players` WHERE nickname IN ('Bryant', 'Sherry', 'John');```
同样，由于 `nickname` 字段也带有索引，所以MySQL在查询时，会先根据索引进行排序，这里是字符串，则会根据字母表顺序排序，即 'Bryant', 'John', 'Sherry'，也就是对应返回结果为 8, 3, 5
<!-- endtab -->

<!-- tab 例4 -->
最后一个例子，```SELECT * FROM `players` WHERE vocation IN ('gardener', 'athlete', 'artist');```
对于这个语句来说，查询条件字段 `vocation` 不带索引，所以MySQL查询时不会根据该字段进行排序，但是由于语句没有用 `ORDER BY` 显示指明排序字段，所以MySQL会默认根据主键字段对检索结果进行排序，所以得到了 3, 5, 8 的查询结果也就不令人意外了
<!-- endtab -->
{% endtabs %}

{% note 'fas fa-text-height' %}
&nbsp;&nbsp;所以，我们可以得到一个结论，当{% label MySQL blue %}在执行没有用{%label 'ORDER BY' purple %}显示指明排序字段的{%label 'WHERE IN' orange %}语句时，会先判断查询字段是否带有索引，如果带有索引的话，会根据该字段进行排序返回最后的查询结果，反之则会根据主键顺序返回最后查询结果
{% endnote %}