---
title: "problems_between_mysql_7_and_8"
date: 2024-02-29T14:25:30Z
# weight: 1
# aliases: ["/first"]
tags: ["MySQL"]
author: "IAKSH"
# author: ["Me", "You"] # multiple authors
showToc: false
TocOpen: false
draft: false
hidemeta: false
comments: false
description: "比较早期的一篇文章，可能存在问题"
disableHLJS: true # to disable highlightjs
disableShare: false
disableHLJS: false
hideSummary: false
searchHidden: true
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowWordCount: true
ShowRssButtonInSectionTermList: true
UseHugoToc: true
editPost:
    URL: "https://github.com/IAKSH/iaksh.github.io/content"
    Text: "Suggest Changes" # edit text
    appendFilePath: true # to append file path to Edit link
---

网上很多文章都是 7. X 甚至 5. X 写的，然而 mysql 在 8.0 的时候有一次比较大的更新，造成了一些差异：
<!--more-->
# 1, 不用显式导入驱动
不需要使用下述代码手动导入驱动：
```java
class.forName("com.mysql.jdbc.Driver");
```
这其实是因为 JDK 1.6 引入了 JDBC 4，会自动加载驱动类。
# 2, SQL 语法差异
`groups` 变成了关键字，如果有一个键叫这个名字，需要用反引号包裹起来，比如：
```sql
SELECT groups,name,password FROM userlist WHERE name = "Admin", password="123456";
```
需要变更为
```sql
SELECT `groups`,name,password FROM userlist WHERE name = "Admin", password="123456";
```
或者可以更进一步，用反引号包裹所有键名。