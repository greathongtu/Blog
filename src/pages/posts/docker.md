---
layout: '../../layouts/MarkdownPost.astro'
title: 'Docker Usage (with mysql)'
pubDate: 2023-03-08
description: '使用 docker 创建 mysql image 并使用的示例'
author: 'Aaron'
cover:
    url: '/preview/docker.png'
    alt: 'cover'
tags: ["docker", "mysql"]
theme: 'light'
featured: false
---

# docker

```bash
# list docker images
docker images
# pulls and start, you can also docker pull first
docker run redis:4.0 
# list running containers (-a means list all running and stopped container)
docker ps
# stop the container
docker stop
# start the stopped container
docker start
# interactive terminal, xxx is docker id from docker ps
Docker exec -it xxx bash
```

## docker 使用 mysql 流程
```bash
# check if docker runs
docker --version
docker pull mysql
# check existing images
docker images
# name is whatever you like. first is host port, then docker port, -d is detached mode
docker run --name mysql -p 80:80 -e MYSQL_ROOT_PASSWORD=123456 mysql

# find docker id of the container
docker ps
docker exec -it 7a767a5565ce /bin/bash

# then inside docker, first login to mysql
mysql -u root -p123456
#inside mysql
show tables;
create database yaojun;
use yaojun
create table department(id int primary key auto_increment, name varchar(20), phone int);
select * from department limit 100;
insert into department(id, name, phone) values(01, 'Tom', '131333140');
insert into department(id, name, phone) values(02, 'Jerry','23');
select * from department limit 100;

drop table department;
drop database yaojun
```