title: "mongodb课堂笔记"
date: 2015-05-23 07:44:03
categories: 学习笔记
tags: [mongodb,索引]
---

#索引
##什么是索引
特殊的数据结构，按照顺序保存文档中的一个或多个指定。
使用B-Tree索引，方便范围查询和匹配查询。
![索引建立原理][1]
各种类型都可以建立索引，比如字符串可以按照ASCII码建立
#建立索引
（一下代码为在线课堂听课时，一边听一边打在记事本上的，所以一下代码仅供参考）
<!--more-->
##插入数据
```
for(var i=0;i<300000;i++){
    db.persons.insert(name:i);
}
```
##创建匿名索引
```
db.persons.ensureIndex({name:1})//从小到大排列
```
##创建命名索引
```
db.persons.ensuerIndex({name:1},{"name":"indexName"})
```
(PS:前面的name表示的是数据的属性，后面的"name"表示的是索引的属性)

##分析索引的执行过程
```
db.persons.find({name:100000}).explain();
```
使用索引前后对比

![使用索引前][2]
![使用索引后][3]
##指定使用的索引
```
db.persons.find({name:1}).hint({name:-1}).explain();
```
##删除所有索引
```
db.runCommand({dropIndexes:"person",index:"*"});
```
##多键索引
自动对数组进行索引
```
db.person.insert({hobby:['basketball','
football','pingpang']});
db.person.ensureIndex({hobby:1});
db.person.find({hobby;'football'},{hobby:1，_id:0}).explain();//find的第二个参数表示是否返回某个字段

```
##复合索引
查询条件不止一个，需要用复合索引
```
db.p.insert({a:1,b:2})
db.p.insert({a:2,b:3})
db.p.find({a:1,b:2}).explain();
db.p.ensureIndex({a:1,b:1});
db.p.find({a:1,b:2},{a:1,_id:0}).explain();
```
##过期索引
在一定的时间后会过期，过期后，相应的数据被删除
用于session、日志、缓存、临时文件
```
db.p2.insert({time:new Date()});
db.p2.insert({time:new Date()});
db.p2.ensureIndex({time:1},{expireAfterSeconds:10});
```
注意：
1，索引字段的值，必须是Date对象，不能是其他类型，比如时间戳
2，删除时间不精确，每60秒跑一次，删除也是需要时间的，所以会存在一些误差

##二维索引
空间索引，可以查询一定范围内的地理坐标
![二位索引][4]
```
for(var i=1;i<=10;i++){
	for(var j =1;j<=10;j++){
    	db.map.insert({gis:{x:i,y:j}});
    }
}
db.map.ensuerIndex({gis:'2d'});//注意此处类型为2d
```

###查询距离[1,1]最近的点
```
db.map.find({gis:{$near:[1,1]},{gis:1,_id:0}}).limit(4);
```
###查询以[1,1]和[3,3]为对角线的矩形里的所有点
```
db.map.find({gis:{$within:{$center:[[1,1],[3,3]]},{gis:1,_id:0}}})
```
###查询以圆心[1,1]，半径为1的圆形里面的所有点
```
db.map.find({gis:{$within:{$center:[[1,1],1]}},{gis:1,_id:0}})
```
###多边形
```
db.map.find({gis:{$within:{$polygon:[[2,1],[1,3],[3,1]]}},{gis:1,_id:0}})
```
#索引注意事项
1,1为正序，-1为倒序
2,索引虽然可以提升查询性能，但是会降低插入效率
3,索引一定要建立的合理


#主从复制

主从复制就是一个简单的数据库同步备份的集群技术
1,集群中需要指定主服务器
2,还需要从服务器，从服务器需要制定谁是他的主服务器

##配置
启动主服务器
mongod --dbpaht=/data/mongodb/master --port=8000 --master
启动从服务器
mongod --dbpath=/data/mongodb/slave --port=8001 --slave --source localhost:8000

##从服务器需要指定的重要选项
- only 指定复制的数据库
- slavedelay 主库向从库同步时的延迟时间
- oplogSize 主节点操作记录存储到Local的oplog里，从服务器从主服务器上取回这个日志文件然后更新自己的数据库
- autoresync是否自动同步所有数据，如果为true则自动同步所有数据，如果为false则只同步主从关系建立后的数据

#副本集
它是一个集群
当主服务器宕机后，其他的从服务器会根据权重算法选举出来一台从服务器作为新的主服务器，原主服务恢复后变成了从服务器，继续加入到当前的集群中

![副本集原理图][5]
![副本集原理图2][6]
主从的区别

从服务器默认shell下不能查询（可设置slaveOK=true或者用插件）

(PS:由于mongodb建立会之间占用一大块空间，所以在本地虚拟机进行测试的情况下请预留出足够的空间)
```
mkdir master
mkdir slave1
mkdir slave2
mongod --dbpath=/data/mongodb/master --port=8000 --replSet=groups --noprealloc
mongod --dbpath=/data/mongodb/master1 --port=8001 --replSet=groups --noprealloc
mongod --dbpath=/data/mongodb/master2 --port=8002 --replSet=groups --noprealloc
//此时没有指定master，会自动选举

db.runCommand({replSetInitiate:{'_id':'groups',members:[{_id:1,host:'127.0.0.1:8000'，priority:0},{_id:2,host:'127.0.0.1:8001'},{_id:3,host:'127.0.0.1:8002'}]}})
```
其中：
priority0-1000
0代表副本节点
1-1000是常规节点
arbiterOnly:true
另外还有一些其他参数

- standard 常规节点，参与投票，并且有可能成为主节点
- passive 副本节点 参与投票，不能成为主要节点，进行复制
- arbiter 仲裁节点 只参与投票，不能成为主节点，不能复制
  [1]: http://7xj22g.com1.z0.glb.clouddn.com/zhufeng%E7%B4%A2%E5%BC%95.jpg
  [2]: http://7xj22g.com1.z0.glb.clouddn.com/zhufeng%E7%B4%A2%E5%BC%952.png
  [3]: http://7xj22g.com1.z0.glb.clouddn.com/zhufeng%E7%B4%A2%E5%BC%953.png
  [4]: http://7xj22g.com1.z0.glb.clouddn.com/zhufeng%E7%B4%A2%E5%BC%954.png
  [5]: http://7xj22g.com1.z0.glb.clouddn.com/zhufeng%E5%89%AF%E6%9C%AC%E9%9B%86.png
  [6]: http://7xj22g.com1.z0.glb.clouddn.com/zhufeng%E5%89%AF%E6%9C%AC%E9%9B%862.png
