---
title: redis和redis模块
date: 2019-12-13 11:41:22
tags:
  - DB-redis
categories:
  - Database
---
### **redis介绍**

  0. mysql、oracle、sql server都是关系型数据库,都有通用的操作语言sql
  redis、mongodb是主要的两个非关系型数据库，
  1. 非关系型数据库nosql是一类新出现的数据库（not only sql），其特点有如下：
    + 不支持sql语法，存储解构采用键值对的形式储存
    + 没有通用的语言，每一个nosql都有自己都有的api和语法：
  2. nosql和sql的比较，再复杂场景下建议使用关系型数据库，sql对事务支持的非常完善，nosql基本不支持事务
  所谓事务：（一组sql操作要么都成功要么都失败）
  3. redis特性：支持书库的持久化，不仅仅支持简单的键值对类型数据，支持数据的备份，
  redis的性能极高，redis的所有操作都是基于原子性的，redis还支持发布订阅通过key过期等等操作：
  4.应用场景：用来做缓存，redis中的数据都是存放到内存中的，可以再某些特定的场景下替代一个传统的数据库，比如社交的应用，
  再一些大型的系统，实现一些特定的功能，session共享、购物车等等


### redis在linux上的安装
  1. 下载，解压，编译:
    $ wget http://download.redis.io/releases/redis-5.0.4.tar.gz
    $ tar xzf redis-5.0.4.tar.gz
    $ cd redis-5.0.4
    $ make
    二进制文件是编译完成后在src目录下. 运行如下:$ src/redis-server
    ==》redis在window下安装进入redisgithub官网就下载最新版msci即可
    进行redis的相关配置操作：

  2. redis服务端在window下的启动：
    + C:\Program Files\Redis 进入redis安装目录
    + redis-server.exe redis.windows.conf  运行redis即可
    + 客户端通过redis-cli命令直接链接服务器端

### 字符串命令
  1. 默认redis有16个数据库：，且默认链接到第一个数据库，select index（索引）链接指定数据库
  2. redis存储的数据结构
  键的类型是字符串、值得类型分为五种：字符串，哈希，集合，有序集合
  数据库得操作行为：保存、修改、删除、获取，
  3. 注意redis是基于二进制安全得，这意味着类字符串类型可以接收任意类型：
  get 键  命令获取值，
  set 键 值 设置键值
  mset 键 值 键 值  命令可以一次性设置多个键值，
  setex 键 时间 值  命令可以设置有时间限制得值，
  append 键 值 可以往键里面追加值

### 键命令
  1. keys * 一次性查看所有键
  2. exists 键 判断键是否存在，存在返回1，不存在返回0
  3. type key 查看key对应得value类型
  4. del 键，用于删除多个键
  5. expire 键 时间  用于设置键对应得时间，单位为秒
  6. ttl 键，查看键还剩下得有效时间（未设置返回-1，过期返回-2）

### hash命令：
  1. 用于存储对象，对象得结构为属性、值：
  2. hset  key  属性 属性值值，创建一个hash存储对象：
  3. hmset key 属性 属性值 创建多个对象 
  3. hkeys u 查看hash键得所有属性
  4. hget 键 属性 获取一个has键得属性
  5. hmset 键 属性1、属性2
  6.  hvals 键 获取hash键得所有属性得属性值
  7. hdel 键 属性1，属性2 一次删除一个或者多个hash键得属性得值

### list命令
  1. lpush 键 值1，值2，值3 从左侧往一个列表插入数据
  2. lrange 键 开始下标 结束下标 查看指定下表区间得列表得值（o表示开始下表，-1表示结束下标）
  3. rpush 键 值1，值2 ，值3从右侧往一个列表插入数据
  4.linsert 键 before/after  现有值 新值  在指定得元素前面或者后面插入一个元素：
  5. lset 键 下标 值 重置指定列表类型键得下标得值
  6. lrem 键  数量m  值 将类型为列表得键的前m次出现为某只的元素移除掉
  注意：如果m=0表示移除所有，>大于0表示从右往左移除,小于表示从左往右移除

### set无序集合命令
  1. sadd 键 值1，值2，值3
  2. smembers 键 查看集合类型的键的所有元素
  3. srem 键 值  删除集合类型的键的某个值，可以删除多个

### zset有序集合命令
  1. zadd 键 权重 值 权重 值
  2. zrange 键 起始下标 结束下标 查看有序集合类型键的指定权重之间的值   zadd rs  2    zhansan 5 lisi 8 wangwu 4 feiq
  3.  zrangebyscore 键 开始权重 结束权重 返回指定权重之间的数据
  4. zrem 键 值  删除  删除类型为有序集合的键的某个值
  5. zremrangebyscore  键 开始权重 结束权重 删除指定权重之间的数据



### redis 和python的交互使用
  注意：以下rr是reids链接类的实例化对象
  1. 依赖于第三方包redis pip install redis
  2. 导入模块 from  redis import * 中的所有属性和方法
  3. 通过StrictRedis（缺省参数域名，缺省参数端口，缺省参数第几个数据库）类的实例化对象链接我们的数据库
  4. rr.set('name','sadfsafsad')   set方法相当于字符串命令中的set命令，第一个参数为字符串类
  型的键，第二个参数为字符串值  注意：该返还值为布尔值，表示是否设置成功：
  5.  res  = rr.get('name')  用于获取字符串类型的键的值
  6. rr.delete（多值元组参数键）  该方法的返回值表示删除了几个字符串类型的键
  7. rr.keys（）方法获取到数据库中的所有的键，返回值为一个列表

  8. redis中存储session的相关配置工作：
  在数据库的配置文件夹下做出如下配置信息：
  SESSION_ENGINE='redis_sessions.session'
  SESSION_REDIS_HOST = 'localhost'
  SESSION_REDIS_PORT = 6379
  SESSION_REDIS_PASSWORD = ''
  SESSION_REDIS_DB = 2
  SESSION_REDIS_PREFIX  = 'session'

  这样当我们设置了session之后，会在我们redis数据库里面生成一个字符串类型的键：

### redis集群
  1. 集群：是一组相互独立的通过高速网络互联的计算机共同对外服务器构成一个服务器整体
  2. redis集群分为软件层面（一台电脑开启多个redis服务）和硬件层面：（多台电脑每台开启一个或多个redis服务）
  3. 软件层面：（一台电脑开启多个·redis服务器；）