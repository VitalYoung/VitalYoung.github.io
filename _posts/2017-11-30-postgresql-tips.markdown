---
layout: post
title: "postgres使用中的一些方法"
date: 2017-11-30 13:58:00 +0800
categories: postgresql SQL
---

1、清空数据库，删除所有表
====


`psql -U$DB_USER -d $DB_NAME_STAGING -h $DB_HOST -t -c "select 'drop table \"' || tablename || '\" cascade;' from pg_tables where schemaname = 'public'" | psql -U$DB_USER -d $DB_NAME_STAGING -h $DB_HOST
`

2、设置数据库本地访问不需要输入密码
====


```shell
$ sudo -u postgres bash
$ vi ~/9.3/data/pg_hba.conf # 设置local对应的METHOD为trust
$ systemctl restart postgresql-9.3.service # 重启服务

```

3、设置数据可以远程访问
====

```shell
$ sudo -u postgres bash
$ vi ~/9.3/data/postgresql.conf  # 设置listen_addresses='0.0.0.0'
$ systemctl restart postgresql-9.3.service # 重启服务

```
