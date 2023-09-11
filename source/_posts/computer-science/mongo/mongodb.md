---
title: MongoDB
date: 2021/12/24
categories:
- [计算机科学, MongoDB]
math: true
mermaid: true
quiz: true
# sticky: true
cover: assets/wallpaper-2572384.jpg
tags:
---

# MongoDB

- ## **将MongoDB加入到Windows服务中**
  
    - **创建服务:**
      
        ```bash
        mongod --dbpath "D:\MongoDbData" --logpath "D:\MongoDbData\log\MongoDB.log" --install --serviceName "MongoDB"
        ```
        
    - **启动MongoDB：**
      
        ```bash
        net start MongoDB
        ```
        
    - **删除服务：**
      
        ```bash
        mongod --dbpath "D:\MongoDbData" --logpath "D:\MongoDbData\log\MongoDB.log" --install --remove "MongoDB"
        ```
        
    - **停止服务：**
      
        ```bash
        net stop MongoDB
        ```
        
    
- ## **导出集合**
  
    mongoexport.exe -d db_name -c collection_name -o d:\data\test.json
    
    [https://blog.csdn.net/G1Apassz/article/details/43837331](https://blog.csdn.net/G1Apassz/article/details/43837331)
    
- ## **导入文件**
  
    ```bash
    mongoimport -d dbs -c col --file path
    #导入csv文件
    mongoimport -d Testdb1 -c score --type csv --headerline --ignoreBlanks --file test.csv
    ```
    
- ## **数据库操作**
  
    查看版本： `db.version();`
    
    创建数据库: `use DATABASE_NAME`
    
    查看当前数据库: `db`
    
    查看数据库(空库不在列表内）: `show dbs`
    
    删库: `db.dropDatabase()`
    
- ## **集合操作**
  
    创建集合: `db.createCollection(name, options(可选项))`
    
    删除集合: `db.collection.drop()`
    
    查看集合: `show collections` 或者 `show tables`
    
    删除全部数据（集合并不会删除）: `db.col.remove({})`
    
    save与insert区别：save可以替换已有的文档，insert不能。
    
- ## **基本知识点（学习通测试题）**
  
    MongoDB数据文件的存储格式为：BSON
    
    MongoDB适合网站数据，SQL，缓存
    
    **mongodb的存储引擎：WiredTiger，MMAPv1和In-Memory。**
    
    NoSQL全称：NotOnlySQL
    
    默认端口：27017
    
    NoSQL特点：（没有一个明确的范围和定义，以下是mongodb书上的）
    
    1. 可弹性扩展
    2. 大数据量，高性能
    3. Base特征
    4. 灵活的数据模型
    5. 高可用
    
    属于NoSQL的数据库种类：**HBase、Redis、MongoDB、Couchbase、LevelDB**
    
    在MongoDB的集合中相当于关系的key是ObjectId；
    
    使用CreateCollection创建固定集合时必须添加参数：capped
    
    数据库备份命令:mongodump
    
    gridfs定义及原理
    
    是MongoDB中存储和查询超过BSON文件大小限制（16M）的规范
    
    GridFS将文件分成多个块，每个块作为一个单独的文档
    
    在MongoDB的固定集合中如果被占满，再插入新文档时，固定集合会自动将最老的文档从集合中删除。
    
    对目标集使用insert方法，插入一个文档，这个操作**不一定**会给文档增加一个"_id"键，让后将其保存到MongoDB中。
    
    空的查询文档{}会匹配集合的全部内容。要是不指定查询文档，默认就是{}。
    
    update修改字段名称用$rename；
    
    MongoDB服务器可以利用复制集部署多个集群，好处是：复制时将数据自动同步到多个服务器中。
    
    MongoDB中副本集的方式可以利用仲裁机制将从节点推举成新的主节点。
    
    MapReduce中map函数是映射，reduce函数是归并
    
    为了维护集合的最新视图，每个成员每隔两秒钟就会系那个其他成员发送一个请求，这个请求是心跳请求。
    
    MongoDB中部署一个分片集群需要三部分，包括Shard Server, Config Server和Route Server。
    
    如果在创建唯一索引时已经存在了重复项，可以通过dropDups参数消除重复文档。
    
    MongoDB不仅支持自动故障恢复，还支持自动分片
    
    聚合数据模型是MapReduce；
    
- **remove与drop区别**
  
    remove：删除所有文档，但是保留索引（index）文件
    
    db.system.indexs.find()查询索引依然存在
    
    drop：真*删除所有文档
    
- ## **find系列**
  
    结构化查询
    
    ```bash
    db.col.find().pretty()
    ```
    
     查询在1997年1月1日之后出生的人
    
    ```bash
    db.col.find({"date":{$gte:new Date(1997,1,1)}})
    ```
    
    大小关系
    
    ```bash
    (>) 大于 - $gt
    (<) 小于 - $lt
    (>=) 大于等于 - $gte
    (<= ) 小于等于 - $lte
    (!=) 不等于 - $ne  
    ```
    
    相关语法(`$not` 也是这个语法推荐`$ne`)
    
    ```bash
    db.col.find({age:{$gte:25, $lte:27}})
    ```
    
    语法默认是and，
    
    `$or`或者`$​nor`用法
    
    ```bash
    db.col.find({$or:[{},{}]})
    ```
    
    `$in`或`$​nin`(对in来说只要里面有一个符合，就列出来)
    
    ```bash
    db.col.find({age:{$in:[12, 13]}})
    db.employee.find( { interest: {$in : [ ‘看电影’ , ‘画画’]}})
    ```
    
    自定义显示属性列(0:不显示，1:显示，id默认显示)
    
    ```bash
    db.col.find({}, {date:1})
    ```
    
    `$mod`
    
    ```bash
    find("$mod":[数字，余数])
    ```
    
    `$exists`判断字段是否存在(true/false)
    
    ```python
    db.users.find({age: {$exists: true}});
    ```
    
    limit
    
    ```bash
    # 只显示前5条数据
    find().limit(5)
    ```
    
    skip   查第6-10条数据
    
    ```bash
    find().limit(5).skip(5) #跳过5条数据，从第六条开始
    ```
    
    sort() : [1(升序), -1(降序)]
    
    ```bash
    find().sort({age:1})
    或者直接sort({age:1})（老师说的，实际上并不行）
    ```
    
    count()
    
    ```bash
    1. 推荐
    db.col.count(查询条件)
    2. 其他方式(别在count内写，没用)
    db.col.find(查询条件).count()
    ```
    
    - 针对数据处理的字段
      
        `$all` (来匹配包含数组所有元素的文档，元素顺序不影响结果)
        
        ```bash
        db.employee.find( { interest: {$all : [ ‘看电影’ , ‘画画’]}})
        ```
        
        `$size` (查询指定长度的数组)
        
        ```bash
        查询有五种爱好的人
        db.employee.find( {interest : {$size : 5}})
        ```
        
        `$slice` (用于指定查询结果中要显示的数组元素，**放在find 的第二个参数中**)
        
        ```bash
        1.
        find( {查询条件} ，{field : {$slice : value}})
        value取值：整数n, 正数显示数组前n个元素，负数显示数组后n个元素
        2.
        db.num.find({}, {num:{$slice:[0, 3]}})   #正数从第n+1个元素，负数从倒数第n个开始，往后显示count个元素
        3.
        $slice:0      #不显示数组内容
        ```
        
        `$elemMatch` （查子文档）
        
        ```bash
        1.
        db.neiqian.find({parents:{$elemMatch:{job:"a"}}})
        2.
        db.neiqian.find({"parents.job":"a"})
        3.与逻辑运算符同用
        db.neiqian.find({$or:[{"parents.job":"a"},{"parents.job":"b"}]})
        ```
        
        查询数组内部数据个数大于等于4的
        
        ```bash
        db.num.find({$where:"this.num.length>=4"})
        ```
        
    - 统计所有人的指定数组的元素个数
      
        ```bash
        
        > db.mycollection.insert({'foo':[1,2,3,4]})
        
        > db.mycollection.insert({'foo':[5,6,7]})
        
        > db.mycollection.aggregate({$project: { count: { $size:"$foo" }}})
        
        { "_id" : ObjectId("5314b5c360477752b449eedf"), "count" : 4 }
        
        { "_id" : ObjectId("5314b5c860477752b449eee0"), "count" : 3 }
        ```
        
    - 正则的相关用法
      
        ```bash
        # 查询以a字符开头的元素
        db.num.find({name:/^a/})
        # 查询以b字符结尾的元素
        db.num.find({name:/b$/})
        # 同时具有上方两个条件查询（注；必须以结尾语法查询开始，具体如下）
        db.num.find({name:/b$/,name:/^a/})
        # 如果知道中间相差的具体有几个字符数可以用"."充当中间的字符数，例如：
        db.num.find({name:/^a..b$/})
        { "_id" : ObjectId("61695d44b1d6dee2a6ab7c31"), "name" : "assb" }
        # 模糊查询（包含伟字就行）
        db.student.find({sname:/^.*伟.*$/},{_id:0, sname:1})
        # 注：另一种语法格式，如下：
        db.posts.find({tags:{$regex:"run"}})
        ```
        
        ```bash
        模糊查询中正则表达式中/i,/g,/ig,/gi,/m的区别和含义
        
        /i (忽略大小写)
        /g (全文查找出现的所有匹配字符)
        /m (多行查找)
        /gi(全文查找、忽略大小写)
        /ig(全文查找、忽略大小写)
        ```
        
    
- ## **update系列**
  
    ```bash
    update(query, update, upsert, multi, writeConcern)
    query : update的查询条件，类似sql update查询内where后面的。
    update : update的对象和一些更新的操作符（如$,$inc...）等，也可以理解为sql update查询内set后面的
    upsert : 可选，这个参数的意思是，如果不存在update的记录，是否插入objNew,true为插入，默认是false，不插入。
    multi : 可选，mongodb 默认是false,只更新找到的第一条记录，如果这个参数为true,就把按条件查出来多条记录全部更新。
    writeConcern :可选，抛出异常的级别。
    ```
    
    `$set` 只更新字段(有则修改，无则追加）
    
    ```bash
    db.person2.update({birthday:"1996-02-14"},{$set:{birthday:"1996"}})
    ```
    
    `$inc` 针对数值型的键进行加减运算
    
    ```bash
    	db.mdb.update({name:"张三"},{$inc:{age:-2}})
    ```
    
    `$unset` 删除指定key
    
    ```bash
    db.mdb.update({name:"张三"},{$unset:{age:1}})
    ```
    
    - 针对数组
      
        `$push` 无条件追加
        
        ```bash
        db.mdb.update({name:"张三"},{$push:{course:"sdsd"}})
        ```
        
        `$pushAll `追加多个
        
        ```bash
        db.mdb.update({name:"张三"},{$pushAll:{course:["wen","quan"]}})
        ```
        
        `$addToSet` 若是存在则不插入
        
        ```bash
        db.mdb.update({name:"张三"},{$addToSet:{course:"sds"}})
        ```
        
        `$pop`n为删除最后n个，-n为删除正数n个
        
        ```bash
        db.mdb.update({name:"张三"},{$pop:{course:1}})
        ```
        
        `$pull` 删除指定字段   `$pullAll`与`$pushAll`同理
        
        ```bash
        db.mdb.update({name:"张三"},{$pull:{course:"sdsd"}})
        ```
        
    
    `$rename` 对字段重命名
    
    ```bash
    {$rename:{old_field_name:new_field_name}} #前面可以不加引号后面必须要加
    db.mdb.update({name:"张三"},{$rename:{course:"sss"}})
    ```
    
- ## **aggregate系列**
  
    - `$group`相关   **（将集合中的文档分组，可用于统计结果）**
      
        `$sum`、`$avg`、`$max`、`$​​min`相关语法：
        
        ```bash
        db.epms1.aggregate([{$group:{_id:"$job", num:{$sum:"$salary"}}}])
        ```
        
        `$addToSet`、`$​push`与update系列规则相同
        
        ```bash
        db.mycol.aggregate([{$group : {_id : "$by_user", url : {$push: "$url"}}}])
        ```
        
        **将所有人员信息全部push进去(`$$ROOT`)**
        
        ```bash
        db.persons.aggregate([
            { 
                $project : {
                 _id : 0 ,
               }
            },
           {
                $group:{
                    _id:{country:"$country", m: "$m"},
                total:{$push:"$$ROOT"}
                }
           },
            {
                $match:{
                    "_id.m":{
                        $gt:90
                    }
                }
            }
        ]);
        ```
        
        `$first`(`$​last`)根据资源文档的排序获取第一个(最后一个)文档数据。
        
        ```bash
        db.mycol.aggregate([{$group : {_id : "$by_user", last_url : {$last : "$url"}}}])
        ```
        
    - `$project`相关 ** （修改输入文档的结构。可以用来重命名、增加或删除域，也可以用于创建计算结果以及嵌套文档）**
      
        ```bash
        db.epms1.aggregate(
            { $project : {
                _id : 0 ,
                name : 1 ,
            }}
         );
        ```
        
        注：除了_id:0可以使用，其他都必须统一为0或者统一为1，（选择的属性，要么都显示，要么都不显示，不能混用）
        
        [其他操作](https://blog.csdn.net/qq_18948359/article/details/88777314)
        
        **把weight重命名为newWeight**
        
        ```bash
        db.test.aggregate(
            { $project : {
                 _id : 0 ,
                name : 1  ,
                weight : 1 ,
                newWeight : "$weight"
            }}
         );
        ```
        
        **使用`＄add`给weight字段的值加10，然后将结果赋值给一个新的字段:newWeight**
        
        ```bash
        db.test.aggregate(
            { $project : {
                 _id : 0 ,
                name : 1  ,
                weight : 1 ,
                newWeight : { $add:["$weight", 10] }
            }}
         );
        ```
        
    - `$match`相关   **（过滤数据）**
      
        ```bash
        db.articles.aggregate( [
                                { $match : { score : { $gt : 70, $lte : 90 } } },
                                { $group: { _id: null, count: { $sum: 1 } } }
                               ] );
        ```
        
    - `$skip`/`$​limit`相关
      
        ```bash
        db.article.aggregate(
            { $skip : 5 });
        ```
        
    - `$unwind`相关  （将文档中的某一个数组类型字段拆分成多条，每条包含数组中的一个值）
      
        ```bash
        # 源数据
        { "_id" : 1, "item" : "ABC1", sizes: [ "S", "M", "L"] }
        
        db.inventory.aggregate( [ { $unwind : "$sizes" } ] )
        
        # 结果：
        { "_id" : 1, "item" : "ABC1", "sizes" : "S" }
        { "_id" : 1, "item" : "ABC1", "sizes" : "M" }
        { "_id" : 1, "item" : "ABC1", "sizes" : "L" }
        ```
        
    - `$sort`相关
      
        内部用法与find系列相同
        
    - `$geoNear`相关 （输出接近某一地理位置的有序文档）
      
        [参考文档](https://blog.yanjingang.com/?p=1381)
        
        ```c
        db.space.aggregate([
            {"$geoNear": {
                "near": [40, 74], //coordinates: [0,0] 经纬度
                "distanceField": "loc",
        				"query": { "_id":"xxx" }, //查询条件
                "maxDistance": 1,  //指定最大距离,不指定则为最大值
                "num": 2, //返回的行数 ，不写:默认返回所有
                "spherical": true  //2dsphere 必须指定
            }}
        ]).pretty();
        ```
    
- ## **MapReduce**
  
    查询user个数：
    
    ```bash
    db.shop.mapReduce(
       function(){emit(this.user,1);},
       function(key,values){return Array.sum(values);},
       {
          out:{inline:1}
       }
    ).find()
    ```
    
    查询归并后并显示出来：
    
    ```bash
    db.sang_books.mapReduce(
    	 function(){
    		emit(this.name, this.price)
    	 },
    	 function(key, values){
    		return Array.sum(values);
    	 },
    	 {
    		 out:{inline:1}
    	 }
     ).find() # 加上find只会显示文档内容不会显示执行成功信息
    ```
    
    不指定文档的话将存储在临时文档中，指定文档就会输入进去（如果没有就创建）：
    
    ```bash
    var results = db.sang_books.mapReduce(
    	function(){
    	   emit(this.name, this.price)
    	},
    	function(key, values){
    	   return Array.sum(values);
    	},
    	{
    		out:"aa"
    	}
    )
    ```
    
    查询到作者有多本书以逗号分隔：
    
    ```c
    db.sang_books.mapReduce(
        function(){emit(this.name,this.book);},
        function(key,values){return values.join(",");},
        {out:{inline:1}}
    ).find()
    ```
    
    查询每个user的每种sku的数量
    
    ```bash
    db.shop.mapReduce(
       function(){
          emit({user:this.user,sku:this.sku},1);
       },
       function(key,values){return Array.sum(values);},
       {
          out:{inline:1}
       }
    ).find()
    ```
    
- ## **createIndex()索引**
  
    - 索引的查和删
      
        查看集合索引：
        
        ```bash
        db.col.getIndexes()
        ```
        
        查看集合索引大小:
        
        ```bash
        db.col.totalIndexSize()
        ```
        
        删除集合所有索引:
        
        ```bash
        db.col.dropIndexes()
        ```
        
        删除集合指定索引:
        
        ```bash
        db.col.dropIndex("name")
        ```
        
    - 常用索引类型
      
        复合索引：
        
        ```c
        //索引命名 { name: "inventory_idx" } (缺省情况下，索引名以键名加上其创建顺序(1：升序  或者  -1：降序)组合而成。)
        db.products.createIndex( { item: 1, quantity: -1 } , { name: "inventory_idx" } )
        ```
        
        多键索引:
        
        ```c
        //是建在数组上的索引，在mongoDB的document中，有些字段的值为数组，多键索引就是为了提高查询这些数组的效率
        db.classes.insertMany([
             {
                 "classname":"class1",
                 "students":[{name:'jack',age:20},
                            {name:'tom',age:22},
                            {name:'lilei',age:25}]
              },
              {
                 "classname":"class2",
                 "students":[{name:'lucy',age:20},
                            {name:'jim',age:23},
                            {name:'jarry',age:26}]
              }]
          )
        db.classes.createIndex({'students.age':1})
        ```
        
        哈希索引:
        
        ```c
        //就是将field的值进行hash计算后作为索引，其强大之处在于实现O(1)查找，
        //当然用哈希索引最主要的功能也就是实现定值查找，对于经常需要排序或查询范围查询的集合不要使用哈希索引
        db.col.createIndex({name:"hashed"})
        ```
        
    - 常用索引属性：
      
        唯一索引:
        
        ```c
        //用于为collection添加唯一约束，即强制要求collection中的索引字段没有重复值
        //在userinfos的name字段添加唯一索引
        db.userinfos.createIndex({name:1},{unique:true})
        ```
        
        局部索引:
        
        ```c
        //只对collection的一部分添加索引。创建索引的时候，根据过滤条件判断是否对document添加索引，
        //对于没有添加索引的文档查找时采用的全表扫描，对添加了索引的文档查找时使用索引
        //userinfos集合中age>25的部分添加age字段索引
            db.userinfos.createIndex(
                {age:1},
                { partialFilterExpression: {age:{$gt: 25 }}}
            )
        //查询age<25的document时，因为age<25的部分没有索引，会全表扫描查找(stage:COLLSCAN)
            db.userinfos.find({age:23})
        //查询age>25的document时，因为age>25的部分创建了索引，会使用索引进行查找(stage:IXSCAN)
            db.userinfos.find({age:26})
        ```
        
        稀疏索引:
        
        ```c
        //当document包含address字段时才会创建索引
        //创建在address上创建稀疏索引
        　　db.userinfos.createIndex({address:1},{sparse:true})
        ```
        
        TTL索引:
        
        ```c
        // (TTL indexes)是一种特殊的单键索引，用于设置document的过期时间，mongoDB会在document过期后将其删除，
        //TTL非常容易实现类似缓存过期策略的功能
        //添加测试数据
        db.logs.insertMany([
               {_id:1,createtime:new Date(),msg:"log1"},
               {_id:2,createtime:new Date(),msg:"log2"},
               {_id:3,createtime:new Date(),msg:"log3"},
               {_id:4,createtime:new Date(),msg:"log4"}
               ])
               //在createtime字段添加TTL索引，过期时间是120s
               db.logs.createIndex({createtime:1}, { expireAfterSeconds: 120 })
        //logs中的document在创建后的120s后过期，会被mongoDB自动删除
        ```
        
        文本索引：
        
        ```c
        db.collection.createIndex(
          {
             title:'text',
             tags：'text'
          }
        )
        
        搜索一下含有 educoder.net 的文章：
        db.collection.find({$text:{$search:'educoder.net'}})
        ```
        
        - search 后的关键词可以有多个，关键词之间的分隔符可以是多种字符，例如空格、下划线、逗号、加号等，但不能是"-"和"\"，因为这两个符号会有其他用途。搜索的多个关键字是 or 的关系，除非你的关键字包含"-" ``
        - 匹配时不是完整的单词匹配，相似的词也可以匹配到；
    - 地理空间索引：
      
        [参考文档](https://blog.csdn.net/yonggang7/article/details/28109463)
        
        [参考文档2](https://www.docs4dev.com/docs/zh/mongodb/v3.6/reference/reference-operator-query-geoIntersects.html)
        
        建立索引：(也可以建立组合索引)
        
        ```c
        db.places.ensureIndex( { loc : "2dsphere" } )
        ```
        
        查询多边形范围的值：
        
        ```c
        db.places.find( { loc :
                          { $geoWithin :
                            { $geometry :
                              { type : "Polygon" ,
                                coordinates : [ [
                                                  [ 0 , 0 ] ,
                                                  [ 3 , 6 ] ,
                                                  [ 6 , 1 ] ,
                                                  [ 0 , 0 ]
                                                ] ]
                        } } } } )
        ```
        
        查询附近的值:
        
        ```c
        db.places.find( { loc :
                                 { $near :
                                   { $geometry :
                                      { type : "Point" ,
                                        coordinates : [ <longitude> , <latitude> ] } ,
                                     $maxDistance : <distance in meters>
                              } } } )
        ```
        
        查询圆形内的值:
        
        ```c
        //[-88, 30] 为经纬度，  10为半径  球形范围
        db.places.find( { loc :
                          { $geoWithin :
                            { $centerSphere :
                               [ [ -88 , 30 ] , 10 ]
                        } } } )
        //2d 圆形范围
        db.space.find(
            {
              "loc": {
                 $geoWithin: {
                     $center: [ [ 40, 70] , 10 ] 
                 }
              }
            }
        )
        ```
        
        查找矩形范围：
        
        ```c
        // 左上角和右下角
        box = [[39,39],[40,74]]
        db.space.find({"loc":{$within:{"$box":box}}})
        ```
        
        - runCommand方法：
            - geoNear ：我们要查询的集合名称；
            - near ：就是基于那个点进行搜索，这里是我们的搜索点“长沙站”；
            - spherical ：是个布尔值，如果为 true，表示将计算实际的物理距离，比如两点之间有多少 km，若为 false，则会基于点的单位进行计算 ；
            - minDistance ：搜索的最小距离，这里的单位是米 ；
            - maxDistance ：搜索的最大距离
            
            ```c
            db.runCommand({
                geoNear:'locations',
                near:{type:'Point',coordinates:[113.018987,28.201215]},
                spherical:true,
                minDistance:1000,
                maxDistance:8000
            })
            ```
            
    
- ## **创建仓库管理员**
  
    ```bash
    use admin
    # 在数据库admin中，创建管理员用户abc，密码为123，拥有root权限
    db.createUser({user:"abc",pwd:"123",roles:[{role:"root",db:"admin"}]})
    # 验证管理员用户是否存在：返回为1说明成功
    db.auth("abc","123")
    # 在admin数据库内查看创建的用户
    show users
    ```
    
    用新身份进入mongo：
    
    ```bash
    # 先关闭现在有的数据库服务
    use admin 
    db.shutdownServer()
    # 重新启动mongod服务
    mongod --auth --port 27017 --dbpath /data/db --logpath /tmp/mongodb.log --fork
    
    auth: 开启身份验证；
    
    dbpath: 指定数据存放路径；
    
    logpath: 指定日志文件输出路径；
    
    fork: 后台运行
    
    # 管理员登录数据库
    mongo -uabc -p123 admin
    
    # 删除管理员用户abc命令如下：
    db.system.users.remove({user:"abc"})
    ```
    
    - 情况二：在受限的情况下验证身份；
      
        开启身份验证后，我们使用命令`mongo`直接连接数据库，虽然也能成功连接，但是在进行数据库操作如`show dbs`时会受到限制：
        
    
    [https://data.educoder.net/api/attachments/207842](https://data.educoder.net/api/attachments/207842)
    
    这是我们就要进入到`admin`数据库，去进行身份验证：返回数字`1`说明验证成功，`0`说明失败，验证成功后将拥有管理员权限
    
    ```
    use admindb.auth("abc","123")
    ```
    
- **创建普通用户**
  
    创建用户：`user2`，密码：`user2`，对数据库`test2`有读写权限，对数据库`test`有只读权限
    
    ```bash
    use test2
    db.createUser({user:"user2",pwd:"user2",roles:[{role:"readWrite",db:"test2"},{role:"read",db:"test"}]})
    ```
    
    **删除用户**
    
    ```bash
    use test2
    db.dropUser("user2")
    ```
    
- **限制ip访问**
  
    ```bash
    # 关闭服务（先用默认方法启动数据库：mongo ）：
    use admin            #进入admin数据库
    db.shutdownServer()  #关闭服务
    exit                 #退出数据库
    启动服务（只有本机 IP 可以连接数据库）：
    mongod --dbpath /data/db --logpath /tmp/mongodb.log --bind_ip 127.0.0.1 --fork
    # binf_ip ：限制连接的网络接口，可以设置多个，以逗号隔开。
    # 限制端口访问(同样先关数据库服务）
    mongod -port 20000 --dbpath /data/db --logpath /tmp/mongodb.log --bind_ip 127.0.0.1 --fork
    # 连接数据库
    mongo 127.0.0.1:20000
    
    ```
    
- **Profiling 工具**
  
    优化步骤一般是：
    
    - 用慢查询日志（system.profile）找到超过50ms 的语句；
    - 然后再通过 .explain() 解析影响行数，分析为什么超过50ms；
    - 决定是不是需要添加索引
    
    Profiling 有两种开启方式，一种是启动服务时配置启动，一种是 mongoshell 中进行实时配置。
    
    Profiling 级别说明：
    
    - 0：关闭，不收集任何数据；
    - 1：收集慢查询数据，默认是100毫秒；
    - 2：收集所有数据
    
    **全局开启 Profiling:**
    
    ```bash
    可以在 mongod 启动时加上以下参数：
    mongod --profile=1  --slowms=50
    或在配置文件里添加两行:
    profile = 1
    slowms = 50
    ```
    
    **关闭 Profiling 工具:(**只需要将收集慢查询数据的时间设置为0就可以关闭**):**`db.getProfilingLevel(0)`
    
    **mongo shell 中启动配置:**
    
    查看状态： `db.getProfilingStatue()`
    
    查看级别： `db.getProfilingLevel()`
    
    设置级别： `db.setProfilingLevel(1)`
    
    设置级别和时间： `db.setProfilingLevel(1,50)` 时间50ms
    
- **只在linux有效**
  
    返回所有结果：
    
    ```bash
    db.system.profile.find().pretty()
    
    结果：
    {
    			"op" : "insert",         #操作类型，有insert、query、update、remove、getmore、command
          "ns" : "test.items",     #操作的集合
          "command" : {
                  "insert" : "items",
                  "ordered" : true,
                  "$db" : "test"
          },
          "ninserted" : 1,
          "keysInserted" : 1,
          "numYield": 0,     #该操作为了使其他操作完成而放弃的次数。通常来说，当他们需要访问还没有完全读入内存中的数据时，操作将放弃。这使得在MongoDB为了放弃操作进行数据读取的同时，还有数据在内存中的其他操作可以完成
          "locks": {         #锁信息，R：全局读锁；W：全局写锁；r：特定数据库的读锁；w：特定数据库的写锁
                  "Global" : {
                          "acquireCount" : {
                                  "r" : NumberLong(1),
                                  "w" : NumberLong(1)
                          }
                  },
                  "Database" : {
                          "acquireCount" : {
                                  "w" : NumberLong(1)
                          }
                  },
                  "Collection" : {
                          "acquireCount" : {
                                  "w" : NumberLong(1)
                          }
                  }
          },
          "responseLength" : 45,     #返回字节长度，如果这个数字很大，考虑值返回所需字段
          "protocol" : "op_msg",
          "millis" : 60,     #消耗的时间（毫秒）
          "ts" : ISODate("2018-12-07T08:19:11.997Z"),     #该命令在何时执行
          "client" : "127.0.0.1",     #链接ip或则主机
          "appName" : "MongoDB Shell",
          "allUsers" : [ ],
          "user" : ""
    }
    ```
    
    profile 部分字段解释：
    
    - op ：操作类型；
    - ns ：被查的集合；
    - commond ：命令的内容；
    - docsExamined ：扫描文档数；
    - nreturned ：返回记录数；
    - millis ：耗时时间，单位毫秒；
    - ts ：命令执行时间；
    - responseLength ：返回内容长度。
    - **常用的慢日志查询命令**
        - 返回最近的10条记录：
          
            ```bash
            db.system.profile.find().limit(10).sort({ ts : -1 }).pretty()
            ```
            
        - 返回所有的操作，除 command 类型的：
          
            ```bash
            db.system.profile.find( { op: { $ne : 'command'} }).pretty()
            ```
            
        - 返回特定集合：
          
            ```bash
            db.system.profile.find( { ns : 'test.items' } ).pretty()
            ```
            
        - 返回大于5毫秒慢的操作：
          
            ```bash
            db.system.profile.find({ millis : { $gt : 5 } } ).pretty()
            ```
            
        - 从一个特定的时间范围内返回信息：
          
            ```bash
            db.system.profile.find(                    
            {                     
            	ts : {                           
            		$gt : new ISODate("2018-12-09T08:00:00Z"),                          
            		$lt : new ISODate("2018-12-10T03:40:00Z")                          
            	}
            }).pretty()
            ```
            
        - 特定时间，限制用户，按照消耗时间排序：
          
            ```bash
            db.system.profile.find(                    
            {                      
            	ts : {                            
            		$gt : new ISODate("2018-12-09T08:00:00Z"),                            
            		$lt : new ISODate("2018-12-10T03:40:00Z")                           
            	}                    
            },                    
            { user : 0 }                   
            ).sort( { millis : -1 } ).pretty()
            ```
            
        - 查看最新的 Profile 记录：
          
            ```bash
            db.system.profile.find().sort({$natural:-1}).limit(1).pretty()
            ```
            
        - 显示5个最近的事件(这个Windows有反应但是数据不对)：
          
            ```bash
            show profile
            ```
    
- ## **主从复制**
  
    配置文件：
    
    ```bash
    # 8888.conf
    dbpath =                #主数据库地址
    port = 8888             #主数据库端口号
    bind_ip = 127.0.0.1    #主数据库所在服务器
    master = true           #确定我是主服务器
    ```
    
    ```bash
    # 7777.conf
    dbpath =                    #从数据库地址
    port = 7777                 #从数据库端口号
    bind_ip = 127.0.0.1         #从数据库所在服务器
    source = 127.0.0.1:8888     #确定我数据库端口  这个配置项(source)可以用shell动态添加
    slave = true                #确定自己是从服务器
    ```
    
    ```bash
    #启动
    mongod --config 7777.conf
    mongod --config 8888.conf
    ```
    
    主从复制的其他设置项
    
    ```bash
    --only        # 从节点>指定复制某个数据库默认是复制全部数据库
    --slavedelay  # 从节点>设置主数据库同步数据的延迟(单位是秒)
    --fastsync    # 从节点>以主数据库的节点快照为节点启动从数据库
    --autoresync  # 从节点>如果不同步则从新 同步数据库
    --oplogSize   # 主节点>设置oplog的大小(主节点操作记录存储到local的oplog中)
    ```
    
    ```bash
    **从节点**中关于主节点的信息全部存到local的sources的集合中
    只要对集合进行操作就可以动态操作主从关系
    use local
    挂接主节点操作之前只留下从数据库服务
    db.sources.insert({"host":"127.0.0.1:8888"})
    删除已经挂接的主节点操作之前只留下从数据库服务
    db.sources.remove({"host":"127.0.0.1:8888"})
    ```
    
- ## **副本集的搭建（Linux）**
  
    非配置文件：[MongoDB 复制(副本集) | 菜鸟教程 (runoob.com)](https://www.runoob.com/mongodb/mongodb-replication.html)
    
    相关文件配置(可将其配置到对应的数据库地址中，注释的地方修改后就是一个新的副本)：
    
    ```bash
    systemLog:
        destination: file
        path: /data/db1/mongod.log # log path
        logAppend: true
    storage :
        dbPath: /data/db1          # data directory
    net :
        bindIp: 0.0.0.0
        port: 28017                # port
    replication:
        replSetName: rs0
    processManagement:
        fork: true
    ```
    
    ```bash
    # 进入对应环境
    mongo localhost:28018
    # 复制集的搭建
    rs.initiate()
    #  出现：rs0:OTHER>或rs0:SECONDARY> 表示进入复制集 在按回车进入rs0:PRIMARY>
    # 查看状态
    rs.status() 
    # 添加副本节点
    rs.add("主机名:28018")
    # 添加仲裁节点
    rs.addArb("180.76.159.126:27019")
    # 一般情况副本是不允许查询的所以需要查询要开启开启从库查询功能
    rs.slaveOk() 或 rs.slaveOk(true)              # 在从服务器中使用命令 或
    db.getMongo().setSlaveOK() # 在主服务器使用命令
    # 若取消读权限则
    rs.slaveOk(false)
    ```
    
- ## **副本集的搭建（Windows）**
  
    主节点出现故障，其余服务器会被推选出主节点，出现故障的服务器修好后，自动变成副节点
    
    ```bash
    # 三个服务形成闭环
    dbpath = D:\sortware\mongod\02\B
    port= 2222
    bind_ip= 127.0.0.1
    replSet = child/127.0.0.1:3333
    
    dbpath = D:\sortware\mongod\02\C
    port= 3333
    bind_ip= 127.0.0.1
    replSet = child/127.0.0.1:1111
    
    dbpath = D:\sortware\mongod\02\A
    port= 1111 #端口
    bind_ip = 127.0.0.1 #服务地址
    replSet = child/127.0.0.1:2222#设定同伴
    
    # 开启服务
    mongod --config A.conf
    mongod --config B.conf
    mongod --config C.conf
    # 启动shell
    mongo 127.0.0.1:1111
    ```
    
    ```bash
    # 2.初始化副本集
    # 注：备份点不能进行查询操作，只有活跃点(shell会有PRIMARY>)可以进行查询操作，所以应在活跃点进行初始化
    use admin
    db.runCommand({"replSetInitiate":
    {
        "id":'child',  # 与上面child对应
        "members":[
            {
                "_id":1,
                "hostl":"127.0.0.1:1111"
            },
            {
                "_id":2,
                "host":"127.0.0.1:2222"
            },
            {
                "_id":3,
                "host":"127.0.0.1:3333"
            }
        ]
    }})
    # 2.查看副本集状态
    rs.status()
    ```
    
    ```bash
    # 3.节点和初始化高级参数
    standard    常规节点:参与投票有可能成为活跃节点
    passive     副本节点:参与投票但是不能成为活跃节点
    arbiter     仲裁节点:只是参与投票不复制节点也不能成为活跃节点
    # 4.高级参数
    Priority 0到1000之间,0代表是副本节点,1到1000是常规节点
    arbiterOnly :true仲裁节点
    用法
    members:[{
        "_id":1,
        "host":"127.0.0.1:1111",
        arbiterOnly : true
    }]
    # 5.优先级相同时候仲裁组建的规则
    # 会选更新时间最早的数据库
    # 6.读写分离操作>扩展读
    #     6.1一般情况下作为副本的节点是不能进行数据库读操作的
    #         但是在读取密集型的系统中读写分离是十分必要的
    #     6.2设直读写分离
    #         slaveOkay: true
    #         很遗憾他在shell中无法掩饰,，这个特性是被与到mongoDB的
    #         驱动程序中的，在java和node等其他语言中可以完成
    # 7.oplog
    #     他是被存储在本地数据库local中的,他的每一个文档保证这一 个节点操作
    #     如果想故障恢复可以更彻底oplog可已经尽量设置大一些用来保存更 多的操作
    #     信息
    #     改变oplog大小
    #     主库--master --oplogSize size
    ```
    
- ## **头歌上的副本搭建**
  
    ```bash
    mongod1.conf 内容如下：同理配三个并执行
    
    port=27018     #配置端口号
    dbpath=/data/db1     #配置数据存放的位置
    logpath=/logs/mongo/mongod1.log     #配置日志存放的位置
    logappend=true     #日志使用追加的方式
    fork=true     #设置在后台运行
    replSet=YOURMONGO     #配置复制集名称，该名称要在所有的服务器一致
    
    执行： mongod -f /etc/mongod/mongod1.conf
    ```
    
    配主节点并配置仲裁：
    
    ```bash
    config = {
      _id:"YOURMONGO",
      members:[
          {_id:0,host:'127.0.0.1:27018'},
          {_id:1,host:'127.0.0.1:27019',arbiterOnly:true},
          {_id:2,host:'127.0.0.1:27020'},
      ]
    }
    rs.initiate(config)
    ```
    
    **切换 Primary 节点到指定的节点**
    
    ```bash
    mongo --port 27018
    rs.conf()     #查看配置
    rs.status()   #查看状态
    
    # 其中 priority : 是优先级，默认为 1，优先级 0 为被动节点，不能成为活跃节点。优先级不为 0 则按照由大到小选出活跃节点
    # 因为默认的都是1，所以只需要把给定的服务器的 priority 加到最大即可。让27020成为主节点，操作如下
    cfg=rs.conf()
    cfg.members[2].priority=2     #修改priority,members[2]即对应27020端口
    rs.reconfig(cfg)     #重新加载配置文件，强制了副本集进行一次选举，优先级高的成为Primary。在这之间整个集群的所有节点都是secondary
    rs.status()
    ```
    
- ## **头歌的分片集搭建**
  
    **配置文件设置**
    
    ```bash
    mkdir -p /data/shard1/db
    mkdir -p /logs/shard1/log
    mkdir -p /data/shard2/db
    mkdir -p /logs/shard2/log
    mkdir -p /data/shard3/db
    mkdir -p /logs/shard3/log
    mkdir -p /data/config/db
    mkdir -p /logs/config/log
    mkdir -p /logs/mongs/log
    ```
    
    配置文件 (新建在 /etc/mongo 目录下):(其中 shardsvr 是用来开启分片的。)
    
    ```bash
    dbpath=/data/shard1/db
    logpath=/logs/shard1/log/mongodb.log
    port=10001
    shardsvr=true
    fork=true
    
    之后配三个与副本集群同理
    mongod -f /etc/mongo/mongod1.conf
    ```
    
    **config 节点**
    
    ```bash
    mongod --dbpath /data/config/db --logpath /logs/config/log/mongodb.log --port 10004 --configsvr --replSet cs --fork
    ```
    
    连接 route 节点：`mongo localhost:10004`
    
    输入以下命令：
    
    ```bash
    use admin
    cfg = {
      _id:'cs',
      configsvr:true,
      members:[
          {_id:0,host:'localhost:10004'}
       ]
    }
    rs.initiate(cfg)
    ```
    
    **route 节点**
    
    ```bash
    mongos --configdb cs/localhost:10004 --logpath /logs/mongs/log/mongodb.log --port 10005 --fork
    ```
    
    连接上 route 节点：`mongo localhost:10005`
    
    添加分片：
    
    ```bash
    sh.addShard('localhost:10001')
    sh.addShard('localhost:10002')
    sh.addShard('localhost:10003')
    ```
    
    查看集群的状态：分片摘要信息、数据库摘要信息、集合摘要信息等:`sh.status()`
    
    **分片验证:**
    
    ```bash
    连接 route 节点：mongo localhost:10005；
    
    数据量太小可能导致分片失败，这是因为 chunksize 默认的大小是 64MB（ chunkSize 来制定块的大小，单位是 MB ），使用以下代码把 chunksize 改为 1MB 后，插入数据，便可以分片成功。
    
    use config
    db.settings.save( { _id:"chunksize", value: 1 } )
    对集合使用的数据库启用分片：
    sh.enableSharding("test")
    添加索引：
    db.user.ensureIndex({ "uid" : 1})
    分片：
    sh.shardCollection("test.user",{"uid" : 1})
    插入10万条数据（大概需要40s 左右）：
    use test
    for(i=0;i<100000;i++){db.user.insert({uid:i,username:'test-'+i})}
    去各个节点查看数据分布情况：查询的文档条数，加起来正好10万条。
    ```
    

[findAndModify()](https://blog.csdn.net/cunchi4221/article/details/107473544)

[runCommand()](https://blog.csdn.net/u013310075/article/details/25657769)