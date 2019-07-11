---
title: Redis快速入门
copyright: true
date: 2019-07-11 15:22:10
tags: Redis基础
categories: Redis
---
>上班摸鱼之作

## 概览

* 需要做的准备
* Redis连接
* 基本类型
* List类型
* SET类型
* Sorted Sets类型
* Hashes类型

***
<!-- more -->

## 需要做的准备

**[+] 当然是先装个Redis出来啦,非常简单**
**[+] 自行了解一下Redis大概是个什么东西啦**
**[+] <http://try.redis.io/>可以在平台上进行练习啦**

## Redis连接

```sh
redis-cli -h host -p port -a password
```

例:redis-cli -h 127.0.0.1 -p 6379 -a "mypass"

## 基本类型

>1为整形,"1"为字符串,字符串最好用""

```sql
> SET hello "world"     #设置键值
OK
> GET hello             #获取键的值
"world"
> DEL hello             #删除键值
(integer) 1
-------------------------------------------

> SET num 10            #INCR仅对整数类型有效
OK
> INCR num              #自增,结果为11
(integer) 11
-------------------------------------------

> SET time demo
OK
> TTL time              #默认存活时间
(integer) -1
> EXPIRE time 120       #让time存活时间为120秒
(integer) 1
> TTL time              #查看键的存活时间
(integer) 118
> TTL time              #查看键的存活时间
(integer) 112
> TTL time
(integer) -2

##################################################
TTL值-2表示该键已不存在,-1表示永久存在(默认键值TTL均为-1)
##################################################
```

## List类型

>RPUSH,RPOP的R为right.LPUSH,LPOP的L为left
>LRANGE,LLEN中的L为list

```sql
> RPUSH friends Alice   #右推入一个value
(integer) 1
> RPUSH friends Bob     #右推入一个value
(integer) 2
> LPUSH friends Sam     #左推入一个value
(integer) 3

######################################
此时键friends里存有3个值(Sam->Alice->Bob)
######################################
-------------------------------------------

> LRANGE friends 0 -1   #查看列表范围全部结果(-1表示最后一位)
1) "Sam"
2) "Alice"
3) "Bob"
> LRANGE friends 0 1    #查看列表范围内前两位
1) "Sam"
2) "Alice"
-------------------------------------------

> LLEN friends          #查看列表长度(即有几个value)
(integer) 3
-------------------------------------------

> LPOP friends          #推出最左边的值(Sam)
"Sam"
> RPOP friends          #推出最右边的值(Bob)
"Bob"
```

## SET类型

>SET类型为集合,有数学基础的都能大概理解了吧

```sql
> sadd superpowers "flight"         #添加键值
(integer) 1
> sadd superpowers "x-ray vision"
(integer) 1
> sadd superpowers "reflexes"
(integer) 1
> srem superpowers "reflexes"       #移除指定值
1
-------------------------------------------

> sismember superpowers "flight"    #判断键中是否存在该值
(integer) 1                         #结果为布尔值
> smembers superpowers              #列出键的所有值
1) "x-ray vision"
2) "flight"
-------------------------------------------

> sadd birdpowers "pecking"
(integer) 1
> sadd birdpowers "flight"
(integer) 1
> sunion superpowers birdpowers     #组合两个集合
1) "pecking"
2) "flight"                         #不会有重复的值
3) "x-ray vision"

#################################
此类型属于无序集合,对结果没有任何的排序
#################################
```

## Sorted Sets类型

>因此,我们需要能够排序的集合类型,开头字母用Z表示
>你能认出下面这些人分别是谁吗

```sql
> ZADD hackers 1940 "Alan Kay"
(integer) 1
> ZADD hackers 1906 "Grace Hopper"
(integer) 1
> ZADD hackers 1953 "Richard Stallman"
(integer) 1
> ZADD hackers 1965 "Yukihiro Matsumoto"
(integer) 1
> ZADD hackers 1916 "Claude Shannon"
(integer) 1
> ZADD hackers 1969 "Linus Torvalds"
(integer) 1
> ZADD hackers 1957 "Sophie Wilson"
(integer) 1
> ZADD hackers 1912 "Alan Turing"
(integer) 1
-------------------------------------------

> zrange hackers 0 -1
1) "Grace Hopper"
2) "Alan Turing"
3) "Claude Shannon"
4) "Alan Kay"
5) "Richard Stallman"
6) "Sophie Wilson"
7) "Yukihiro Matsumoto"
8) "Linus Torvalds"

####################################################
有序集合同样不会保留重复的值,并会从左往右按照数字的大小进行排序
####################################################
```

## Hashes类型

>哈希类型是一个键值对的集合,一看就适合用来存储对象

```sql
> HSET user1000 name "John Smith"
(integer) 1
> HSET user1000 email "john.smith@example.com"
(integer) 1
> HSET user1000 password "s3cret"
(integer) 1
-------------------------------------------

> HGETALL user1000
1) "name"
2) "John Smith"
3) "email"
4) "john.smith@example.com"
5) "password"
6) "s3cret"
-------------------------------------------

> HMSET user1001 name "Mary Jones" password "hidden" email "mjones@example.com"
OK
> HGET user1001 name
"Mary Jones"
```
