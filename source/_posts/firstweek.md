---
title: firstweek
date: 2026-04-10 08:21:05
tags:
---

# 第一天

## 信息查找
Server字段确定后端是什么服务器、语言和版本

敏感目录
git目录泄露
svn目录泄露
idea工程目录泄露
后台目录泄露

敏感文件
vim备份文件
gedit备份文件
其他备份文件

## HTTP请求
请求头
User-Agent      //伪造浏览器类型
Referer         //伪造来源地址
Cookie          //伪造 Cookie
Host            //伪造主机地址

CTFHub例题：https://www.ctfhub.com/#/skilltree 

请求方式
一般用 burp suite 修改 get 为指定请求方法

302跳转
用浏览器访问index.php发现会302跳转到 index.html
可以用curl访问index.php: curl -v 网址/index.php

HTTP基础认证
其核心是通过HTTP头部的 Authorization 字段传递凭证
'Authorization: Basic <Base64编码后的用户名:密码>'
抓包后用提供的字典爆破，sniper模式选定参数，添加前缀admin，再添加编码base64，爆破即可

## sql注入

常见数据库：MySQL、Oracle、DB2、SqlServer等

### MySQL数据库
{% asset_img MySQL.png %}
information_schema(mysql自带的一张表)

schemata表可以查询所有数据库名称
``` bash
$ select * from information_schema.schemata;
```

tables查询一个数据库中有哪些表
``` bash
$ select * from information_schema.tables where table_schema='redhatdb';
```

columns查询某个数据库中某张表有哪些字段
``` bash
$ select * from information_schema.columns where table_schema='redhatdb' and table_name='cumstom';
```

{% asset_img SQLselect.png %}

https://www.runoob.com/sql/sql-tutorial.html 

数字型注入：用户输入的id字段没有过滤的直接拼接在sql查询语句中，且id没有被引号包围，类型为数字。
可以运用union结合两个select，但要求两个select拥有相同列数
``` bash
$ id=0 union select password from users       #空格的url编码为%20
```

字符型注入：id有引号，需要我们用'or1=1#跳过和注释掉引号，且返回真。

布尔盲注：服务器只会返回用户是否存在。可以用substr字符串截取函数。
substr(要截取的字符串,截取开始的位置,截取长度)
可以用length函数检测长度。用爆破比较高效。
``` bash
$ select id from users where username='' or substr(password,1,1)='3' #
$ 如果结果为false，则第一个字符不为3
```

mysql注释符
#、--空格、--+、%23、(#在get型注入中不能使用)

XFF伪造



