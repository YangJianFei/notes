## node.js redis
```
npm install redis
```

### 基本功能
> 缓存系统
> 数据存储
> 消息中介
> select 0-15数据库
### 连接
```
var redis = require('redis'),
RDS_PORT = 6389,        //端口号
RDS_HOST = '127.0.0.1',    //服务器IP
RDS_PWD = '88888888888888',     //密码 
RDS_OPTS = {},            //设置项
rclient = redis.createClient(RDS_PORT,RDS_HOST,RDS_OPTS);
rclient.auth(RDS_PWD,function(){  
    console.log('redis通过认证');  
}); 

//redis数据库
rclient.set('key','1');//赋值
rclient.expire('key',60);//60秒自动过期

client.set('hello','This is a value');

// 值操作
client.set('hello','This is a value');

client.get('hello',function (err,v) {
    console.log("redis get hello err,v",err,v);
})
```

### 操作列表
```
var client = require('./client');

//先清除数据
client.del('testLists');
client.rpush('testLists','a');
client.rpush('testLists','b');
client.rpush('testLists','c');
client.rpush('testLists','d');
client.rpush('testLists','e');
client.rpush('testLists','1');

client.lpop('testLists',function (err,v) {
    console.log('client.lpop , err , v : ' , err,v);
})

client.rpop('testLists',function (err,v) {
    console.log('client.rpop , err, v ',err,v);
})

client.lrange('testLists',0.,-1, function (err,lists) {
    console.log('client.lrange , err ,lists: ',err,lists);
})
```

### 操作集合
```
var client = require('./client');

client.sadd('testSet', 1);
client.sadd('testSet', 'a');
client.sadd('testSet', 'bb');

//不可重复
client.sadd('testSet', 'bb');

client.smembers('testSet', function(err, v){
    console.log('client.smembers err, v:', err, v);
});
```

### 消息中介
```
// sub.js订阅方
var client = require('./client');

client.subscribe('testPublish');

client.on('message', function(channel, msg){
  console.log('client.on message, channel:', channel, ' message:', msg);
});

// pub.js发布方
var client = require('./client');

client.publish('testPublish', 'message from pub.js');
```

## 命令汇总
### string字符串
```
命令                  行为                                              返回值                     使用示例(略去回调函数)
 set         设置存储在给定键中的值                      OK                         set('key', 'value')
 get         获取存储在给定键中的值                     value/null          get('key')
 del         删除存储在给定键中的值(任意类型)    1/0                        del('key')
 incrby  将键存储的值加上整数increment                                         incrby('key', increment)
 decrby  将键存储的值减去整数increment                                         decrby('key', increment)
 incrbyfloat 将键存储的值加上浮点数increment                                incrbyfloat('key', increment)   
 append  将值value追加到给定键当前存储值的末尾                           append('key', 'new-value')
 getrange    获取指定键的index范围内的所有字符组成的子串            getrange('key', 'start-index', 'end-index')
 setrange    将指定键值从指定偏移量开始的子串设为指定值           setrange('key', 'offset', 'new-string')
```

### list列表
```
命令                    行为                                              返回值                     使用示例(略去回调函数)
 rpush       将给定值推入列表的右端                     当前列表长度      rpush('key', 'value1' [,'value2']) (支持数组赋值)
 lrange  获取列表在给定范围上的所有值          array                       lrange('key', 0, -1) (返回所有值)
 lindex  获取列表在给定位置上的单个元素                                     lindex('key', 1)
 lpop        从列表左端弹出一个值，并返回被弹出的值                     lpop('key')
 rpop        从列表右端弹出一个值，并返回被弹出的值                     rpop('key')
 ltrim       将列表按指定的index范围裁减                                                ltrim('key', 'start', 'end')
```

### set集合
```
命令                  行为                                              返回值                     使用示例(略去回调函数)     sadd            将给定元素添加到集合                      插入元素数量      sadd('key', 'value1'[, 'value2', ...]) (不支持数组赋值)(元素不允许重复)
 smembers    返回集合中包含的所有元素                array(无序)           smembers('key')
 sismenber   检查给定的元素是否存在于集合中 1/0                         sismenber('key', 'value')
 srem            如果给定的元素在集合中，则移除此元素 1/0              srem('key', 'value')
 scad            返回集合包含的元素的数量                                                sacd('key')   
 spop            随机地移除集合中的一个元素，并返回此元素                spop('key')
 smove           集合元素的迁移                                                                 smove('source-key'dest-key', 'item')
 sdiff           返回那些存在于第一个集合，但不存在于其他集合的元素(差集)   sdiff('key1', 'key2'[, 'key3', ...])   
 sdiffstore  将sdiff操作的结果存储到指定的键中                         sdiffstore('dest-key', 'key1', 'key2' [,'key3...])   
 sinter      返回那些同事存在于所有集合中的元素(交集)               sinter('key1', 'key2'[, 'key3', ...])
 sinterstore 将sinter操作的结果存储到指定的键中                        sinterstore('dest-key', 'key1', 'key2' [,'key3...])  
 sunion      返回那些至少存在于一个集合中的元素(并集)               sunion('key1', 'key2'[, 'key3', ...])
 sunionstore 将sunion操作的结果存储到指定的键中                        sunionstore('dest-key', 'key1', 'key2' [,'key3...])  
```

### hash散列
```
命令                  行为                                              返回值                     使用示例(略去回调函数)
 hset            在散列里面关联起给定的键值对      1(新增)/0(更新) hset('hash-key', 'sub-key', 'value')    (不支持数组、字符串)
 hget            获取指定散列键的值                                                           hget('hash-key', 'sub-key')
 hgetall     获取散列包含的键值对                      json                        hgetall('hash-key')
 hdel            如果给定键存在于散列里面，则移除这个键                 hdel('hash-key', 'sub-key')
 hmset           为散列里面的一个或多个键设置值 OK                          hmset('hash-key', obj)
 hmget           从散列里面获取一个或多个键的值 array                       hmget('hash-key', array)
 hlen            返回散列包含的键值对数量                                                hlen('hash-key')
 hexists     检查给定键是否在散列中                 1/0                         hexists('hash-key', 'sub-key')
 hkeys           获取散列包含的所有键                      array                       hkeys('hash-key')
 hvals           获取散列包含的所有值                      array                       hvals('hash-key')
 hincrby     将存储的键值以指定增量增加           返回增长后的值 hincrby('hash-key', 'sub-key', increment) (注：假如当前value不为为字符串，则会无输出，程序停止在此处)
hincrbyfloat    将存储的键值以指定浮点数增加
```

### zset有序集合
```
命令                  行为                                              返回值                     使用示例(略去回调函数)
zadd            将一个带有给定分支的成员添加到有序集合中                zadd('zset-key', score, 'key') (score为int)
zrange      根据元素在有序排列中的位置，从中取出元素
zrangebyscore   获取有序集合在给定分值范围内的所有元素
zrem            如果给定成员存在于有序集合，则移除
zcard           获取一个有序集合中的成员数量      有序集的元素个数    zcard('key')
```

### redis其他常用命令汇总
```
keys命令组
 命令                  行为                                              返回值                     使用示例(略去回调函数)
 del             删除一个(或多个)keys           被删除的keys的数量     del('key1'[, 'key2', ...])
 exists      查询一个key是否存在                             1/0                     exists('key')
 expire      设置一个key的过期的秒数                       1/0                     expire('key', seconds)
 pexpire     设置一个key的过期的毫秒数                  1/0                     pexpire('key', milliseconds)    
 expireat    设置一个UNIX时间戳的过期时间            1/0                     expireat('key', timestamp)
 pexpireat   设置一个UNIX时间戳的过期时间(毫秒) 1/0                    pexpireat('key', milliseconds-timestamp)
 persist     移除key的过期时间                                  1/0                     persist('key')
 sort            对队列、集合、有序集合排序       排序完成的队列等  sort('key'[, pattern, limit offset count])
 
 flushdb     清空当前数据库
```