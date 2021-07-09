---
title: mongodb和pymongo模块
date: 2020-10-13 11:42:45
tags:
  - DB-mongo
categories:
  - Database
---

## **nosql：mongodb**

### mongodb说明
  
  1. mongo的客户端是js运行环境的，我们可以写任意js代码；
  2. mongo里面的对象是js对象，且mongo里面是支持写任何js函数的，
  3. mongodb:查看帮助：mongod -help
    启动：sudo service mongod start
    停止:   sudo service mongod stop
    重启：sudo service mongod restart 
    查看是否启动成功： ps ajx|grep mondod
    查看配置文件： /etc/mongod.conf
    默认端口：27017
    日志
  4. mongo是一个非关系的关系型数据库，非关系型数据库通常是以键值对的形式存在；
  5. mongo是一个扩展性强的数据库，
  6. 安装添加环境变量就可以了
  7. 以上我们只是开启了mongod服务器，我们可以mongo开启客户端就可以使用交互式命令了

### **mongo语法**
##### 对数据库和集合的操作
  1. show databases;
  2. use 数据库名：
  3. db.dropdatabase();删除数据库
  4. 注意：mongodb是没有表这个概念的，一般称作为一个集合：
  5. db.createCollection(name,options)  配置对象的capped属性true值表示设置上限size属6. 性表示10个字节，超过旧值被覆盖，
  7. db.集合名.drop()删除集合；
  8. 数据类型：string，boolean  integer double浮点型 arrays数组 object用于嵌入式的文档，null  timestamp时间错，
  注意：mongod 创建日期的语法如下：new date（xxxx-mm-dd）,
  9. 注意每一个文档都有一个属性，_id保证文档的唯一性，如果我们不提供，mongod内置提供，
  object id即是每个文档的唯一id， 其前四个字节为当前时间搓，下三个为机器id后两个为mongod服务进程id，最后三个为简单增量值；

##### 插入数据的操作
  1. 利用数据库下的test100集合下的insertapi插入一个对象：
    db.test100.insert({"sda":"sdafdsaf"})
    db.test100.insert({"name":"xiaoming"})
  2. db.test100.find()查找集合下的所有数据：所有的对象
    { "_id" : ObjectId("6043ab203f993b072f5e9500"), "name" : "xiaoming" }
    { "_id" : ObjectId("6043acfa3f993b072f5e9501"), "sda" : "sdafdsaf" }
  3. 每一个集合有多个文档，文档就是每次插入的唯一标识，它都带有一个唯一标识_id

11.db.test100.save({'_id':'sad','s':'sa'})保存一个保存一个对象，
注意如果此时id发生冲突将覆盖旧的且不会报错，但是insert插入一个对象可能会冲突报错：

##### 更新数据的操作
1. db.集合名.update(条件，新值，配置参数)
  db.test100.update({name:'xiaoming'},{name:'xiaozhao'})  注意：此时是
2. 把第一个参数条件为name = xiaoming的对象值该为后面这个对象，如果原来还有键值对将丢失，
  即将满足条件的对象完全替换成后面的新对象
3. 注意：db.test100.update({name:"ds"},{$set:{name:"dsafdsa"}});
4. 后面的更新的对象里面的$set字段键表示更新，某些字段，不能直接写新键值对对象；
5. 注意：如果不指定update方法的第三个参数的multi属性为true是不会修改多条记录的，只会替换一条记录，

##### 删除数据的操作
1. db.集合名.remove（条件，配置参数） 
2. 注意：默认请求下会删除集合中的满足条件的记录，如果有配置参数属性justone为true则只删除一条

##### 数据的查询操作
  0. 额外：pretty()方法可以美化输出：
  1. db.test100.find({age:10}) 查找满足脚尖age的所有数据；
  2. db.test100.findOne({age:10})  查找满足条件的一个数据

  3.比较运算符：
  默认是等于：没有运算符：
  $gl,$lt $lte,$gte $ne: 分别表示大于、小于，大于等于，小于等于：
  用法：db.test100.find({age:{$gt:10}})  表示查找所有年纪大于10的所有记录

  4.$in 表示在什么范围、$nin表示不在什么范围   范围运算符运算符号：
  db.test100.find({age:{$in:[12,34,545]})
  5.多条件查询：
  db.test100.find({age:{$gt:50},index:"212"}) 查询多个条件的书

  6.逻辑运算符：
  或逻辑查询：
  db.test100.find(   { $or:[  {age:10},{age:221} ] ,name:"agsse" }  )  
  这是查找age是10或者221的且name值为agsse的数据
  没有逻辑且，直接写多个键值对作为条件即可，
  没有逻辑非，直接$ne表示不等于就行了

  7.支持正则表达式：
  db.test100.find({index:/^abc/})  查找键index的值以abc开头的所有记录：

  8.支持限制和跳跃获取数据：limit(整形数字) 、skip（整形数字） 条约多少个

  9.自定义查询条件，查询条件为js函数，
  {% codeblock %}
  db.test100.find({
      $where:function(){
          return this.age>30
      }
  })
  
  <!-- 注意：自定义的js函数必须要有返回值：$where表示自定义查询条件：
  10.通过第二个配置参数指明需要查看的字段，必须为键值对类型，需要查看则写1，
  不需要查看旧不写 -->

  db.test100.find({},{asd:1})
  <!-- 查看所有的记录但是只查看我们的asd字段，没有该字段的显示空记录， -->
  { "_id" : ObjectId("6043ab203f993b072f5e9500") }
  { "_id" : ObjectId("6043acfa3f993b072f5e9501") }
  { "_id" : ObjectId("6043b1bb5cd69f8fe4a00ee6"), "asd" : "sadf" }
  { "_id" : ObjectId("6043c1a75cd69f8fe4a00ee7"), "asd" : "sad" }
  { "_id" : ObjectId("6043c1cb5cd69f8fe4a00ee8") }
  { "_id" : ObjectId("6043c1e35cd69f8fe4a00ee9") }
  { "_id" : ObjectId("6043c1fa5cd69f8fe4a00eea") }
  {% endcodeblock %}
  11.排序：db.test100.find().sort({age:1})  以age字段对记录进行排序值为1则是升序，值为-1为降序，
  如果没有该排序的字段，则显示在最前面：降序则相反，没有显示在最后面；

  12.find（）方法的第二个参数为配置选项，第一个参数为条件

  13.count（）统计查询的个数：
  db.test100.find().count() 查找所有的记录然后统计个数，注意如果没有条件find（）方法可以省略db.test100.count()
  db.test100.find({age:{$ne:"s"}}).count();

  14..mongod的投影：返回结果的字典
  注意：在查询的字段里面_id默认会显示，我们可以通过_id:0这个配置属性来不显示它(其他字段不能这么设置为0不显示
  这是_id字段的都有写法)，
  db.test100.find({},{name:1,_id:0})

  15.消除重复：对某一字段进行去重，
  db.test100.distinct(“index”) 对index字段进行去重
  > db.test100.distinct("index")
  [ "sadfsa", "sad", "sdaf" ]
  他会返回不重复的值的列表：
  db.test100.distinct("index",{age:{$ne:"s"}})  它的第二个参数是一个键值对的条件，返回值为去重的经过筛选的列表：

  15.mongodb中的表达式有：
  > $sum
  $avg
  $min
  $max
  $push 将结果插入一个数组中
  $first根据查询到的数据获取到第一个
  $last根据查询到的数据获取到最后一个
  $
  $

##### 数据的聚合操作
  1. 聚合aggregate：通常用于进行分组或者过滤等等操作；
  注意：管道的作用就是将上一次的结果作为下一次命令的条件：
  在mongodb中文档处理完毕后，通过管道进行下一次的处理：常用的管道有：

  2. 分组： 通过某个字段进行分组（通过_id指定分组的依据字段，），
  > db.test.aggregate({$group:{_id:"$gender"}})  
  他会返回分组的字段：
  { "_id" : false }
  { "_id" : 0 }
  { "_id" : 1 }
  在分组的时候通过count进行统计，他会返回分组的我们这两个id：
  db.test.aggregate({$group:{_id:"$gender",count:{$sum:1}}})
  { "_id" : false, "count" : 1 }
  { "_id" : 0, "count" : 2 }
  { "_id" : 1, "count" : 3 }
  所以：$group分组命令中的字段，就是我们返回的分组中的字段，
  在分组的使用指定avg字段：然后取平均值{$avg:"$age"}  这里通过age这个字段进行求平均，注意必须使用$
  db.test.aggregate({$group:{_id:"$gender",count:{$sum:1},avg_age:{$avg:"$age"}}})
  注意：我们通常指定分组的_id为null来统计集合中的所有记录：
  db.test.aggregate({$group:{_id:null,count:{$sum:1}}})
  { "_id" : null, "count" : 6 }
  注意：聚合函数的第二个参数$project可以用来对数据进行一些重命名，或者控制显示的字段， 
  {$avg:"$age"}}},{$project:{gender:"$_id",count:"$count",_id:0}}
  db.test.aggregate({$group:{_id:"$gender",count:{$sum:1},avg_age:{$avg:"$age"}}},{$project:{gender:"$_id",count:"$count",_id:0}})
  { "gender" : false, "count" : 1 }
  { "gender" : 0, "count" : 2 }
  { "gender" : 1, "count" : 3 }
  注意：在聚合函数中的project属性中，$_id表示取上一次的_id字段的值，相当于管道符的作用，其他字段加$符号也一样

  2.聚合函数的$match用于过滤数据，输出符合条件的文档，match是管道命令，能将结果交给后一个管道，但是find不可以
  > db.test.aggregate({$match:{age:{$gt:12}}})
  { "_id" : ObjectId("604430205cd69f8fe4a00eed"), "name" : "wanger", "age" : 21, "gender" : 0 }
  { "_id" : ObjectId("604430375cd69f8fe4a00eee"), "name" : "mazi", "age" : 20, "gender" : 0 }
  { "_id" : ObjectId("604430535cd69f8fe4a00eef"), "name" : "sidsi", "age" : 22, "gender" : 1 }
  { "_id" : ObjectId("604431415cd69f8fe4a00ef0"), "name" : "ss", "age" : 21, "gender" : false }
  其后还可以接分组等操作，而find不可以：
  注意：去重不仅可以通过$group进行分组：分组通过所有的字段进行分组依据：
  {_id:{country:"$country",age:"$age"}}  等操作就可以完成去重，通过分组的方式，
  当然了：这也可以通过多键进行分组：
  注意：聚合函数aggregate（操作1，操作2，操作3）的语法：且后面的操作会以前面操作的返回值的基础上进行操作：
  这些操作：可以是分组$group  $match过滤  $project等等操作：
  所以：聚合函数的功能就是聚合一些操作，且后续操作是在前面操作的返回值的基础上进行的；

  3.$sort 用来对记录进行排序输出，$skip跳过操作，$limit进行取记录限制等等操作：
  > db.test.aggregate({$sort:{age:1}})  如下操作按照升序进行排序$sum值为1表示升序，-1表示降序，
  db.test.aggregate({$group:{_id:"$gender",count:{$sum:1}}},{$sort:{count:1}})
  { "_id" : false, "count" : 1 }
  { "_id" : 0, "count" : 2 }
  { "_id" : 1, "count" : 3 }

  4.$unwind等等操作：（它以字段作为值，）
  > 将文档中的某个数组类型的字段拆分为多条，每条包含数组中的一个值，注意这样情况下其他记录没有列表类型的size值将会丢失
  为了防止这种情况（没有size，或者size属性值不是数组），建议如下操作：
  db.test1.aggregate({$unwind:"$size"})
  { "_id" : 1, "item" : "t-shirt", "size" : "s" }
  { "_id" : 1, "item" : "t-shirt", "size" : "m" }
  { "_id" : 1, "item" : "t-shirt", "size" : "l" }
  $unwind分隔字段的第二种用法
  通过preserveNulAndEmptyArrays:true设置为true防止值丢，
  db.test.aggregate({
    &nbsp;&nbsp;$unwind:{
    &nbsp;&nbsp;&nbsp;&nbsp;  path:"$字段",
    &nbsp;&nbsp;&nbsp;&nbsp;  preserveNulAndEmptyArrays:true   
    &nbsp;&nbsp;}
  })
  5.索引的建立操作：
  > 索引的作用：用来提高查询的速度：
  db.test2.ensureIndex({name:1})   
  通过ensureIndex()方法建立索引，并传入一个js对象
  > db.test3.ensureIndex({name:1})
  {
    "createdCollectionAutomatically" : false,
    "numIndexesBefore" : 1,
    "numIndexesAfter" : 2,
    "ok" : 1
  }
  注意：索引需要以一个字段作为索引，字段值为1为升序，字段值为-1表示降序，以js对象的形式书写
  给一个集合建立索引， 
  如果要删除集合建立索引的字段的索引，（注意是删除索引）
  db.test3.dropIndex({name:1})  
  注意：如果建立的升序索引
  查看一个集合中的所有索引：
  db.test.getIndexes();
  [
  &nbsp;&nbsp;  {
  &nbsp;&nbsp;&nbsp;&nbsp;    "v" : 2,
  &nbsp;&nbsp;&nbsp;&nbsp;    "key" : {
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;    "_id" : 1
  &nbsp;&nbsp;&nbsp;&nbsp;    },
  &nbsp;&nbsp;&nbsp;&nbsp;    "name" : "_id_",
  &nbsp;&nbsp;&nbsp;&nbsp;    "ns" : "ddj.test3"
  &nbsp;&nbsp;  }
  ]
  注意：默认情况下索引字段的值并不是唯一的，值可以是相同的，但是我们可以通过配置参数unique属性为
  true建立唯一索引；
  db.test3.ensureIndex({name:1},{unique:true}) 
  建立联合索引的方法：
  db.test3.ensureIndex({name:1,age:1})  建立namehe1age的联合
  注意：数据库的唯一索引也可以进行去重：

### 爬虫中使用pymongo
  0. 爬虫数据去重得主要思路（实现增量式爬虫；）：
  1. 使用url地址进行去重；
    比如帖子得url，一般是唯一得，其内容可能会发生变化；
    一本可以将url存放在redis中，拿到url地址，判断是否重复，如果存在，
    布隆过滤器：用非常小的思路
  2. 根据数据本身进行去重，使用加密算法； 
  3. 数据备份
  4. 数据库的备份
  在python里面使用我们的mongodb数据库需要pymongo模块
  下载pymongo，并导入moongo客户端这个类，
  5.  pymongo使用
  from pymongo import MongoClient
  调用一下类，创建一个实例化对象，并通过字典参数指明域名和端口
  client = MongoClient(host='127.0.0.1',port=27017)
  创建集合，client['数据库名']['集合名']
  collection  = client['test4']['ddj']
  插入单条数据；
  collection.insert_one({"name":"xiaoming","age":1})
  相关的api还有insert_many,
  find()
  注意：findapi返回值是一个游标，每遍历一次取一次值，游标往前移动一位，
  update_one()
  update_many()
  注意：find方法返回的是一个邮编对象，可以通过遍历取到对象中的每一个记录值，
  <pymongo.cursor.Cursor object at 0x000001E1304E1B50>
