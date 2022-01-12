# Redis

## 安装

## 数据类型

### 通用命令

* keys命令：[keys * ]：查寻所有的数据，单例，在开发中不建议使用，会阻塞其他操作。
* dbsiza：查询key的数量。
* exists key ：【exists a】：查询这个key是否存在。
* expire key secodes：给指定的可以设置有效时间，单位为秒。【expire a 60】
* ttl key ：查看当前key的有效剩余时间。返回-1表示永久有效，返回-2表示此key无效。
* persist key ：必须在当前key的有效时间内设置，可以使当前key持久化。
* del key：删除当前key的值。成功返回:1,失败返回:0
* type key：查询当前key的数据类型。
* flushdb：删除redis库
* flushall：删除所有的redis库
* info:查看信息

### Redis为什么怎么快

* 数据存储在内存中

 ### String类型

* 字符串的值最大512M
* 存放的value可以是字符串，数值，二进制，json数据

#### 常用api

* set：新增【set key value】
  * nx：当前key不存在时才能进行添加。【set name gyf nx】 简化：setnx nane gyf
  * xx ：当前key存在时才能进行修改。【set name gyf xx】
  * ex，给当前key设置过期时间。【set name gyf1  ex 10】：改修name的值为gyf1，并添加过期时间为10s ；简化setex name gyf1
* get ：查询value 
* del：删除key对应的value
* incr ：对当前value自增，value值必须为数值类型
* decr ：对当前value自减，value值必须为数值类型
* incrby：z对当前value自增多少。【incrby age 10】
* decrby：对当前value自增多少。【decrby age 10】
  * <font color=#FF0000 >注：当key输入时，库中不存在。则会新增一个对应的key-value</font>
* mset：可以新增多个k-v值；【mset  k1 v1 k2 v2】
* mget：可以换取多个k对应的v值；【mget  k1 k2】
* getset：替换旧值，并返回旧值；【getset k1 newv1】
* append ：将旧值于新值进行拼接；【append k1 ooxx】
* strlen：返回k对应的v值的长度；【strlen k1】
* getrange ：获取k对应的v值所需要的部分；【getrange k1 0 2 】：获取k1从0到2的值
* setrange ：替换k对应的v值所需要的部分；【setrange k1 0 abc 】：修改k1从0开始对应v值长度的值。
* incrbyfloat：对当前value自增多少，注意v的值为小数类型； 

#### 常用场景

1. 缓存：秒杀缓存
2. 

### Hash类型

#### 常用API

一般在String类型的API前加上[h]，就可以改为hash类型

* hget：获取对应k的v值；【hget name:1 name】
* hset：新增或者修改k和所对应的v值；【hset name:1 name gyf age 24】:新增；【hset name:1  age 24】:修改
* hdel ：删除对应k的v值；【hdel name:1 name age】
* hexists :判断是否存在k所对应的v值中的字段【hexists name:1 name】
* hlen ：获取k对应v值的字段数量【hlen name:1】
* hgetall：获取k对应v值所有的字段名和字段值；【hgetall name:1】
* hkeys :获取k对应v值所有的字段名；【hkeys name:1】
* hvals :获取k对应v值所有的字段值；【hvals name:1】

### List类型

双向列表，数据是有顺序的，元素是可以重复的；

左边为头，右边为尾，从左边操作前缀为[l];从右边操作前缀为[r]

pop为取出   数据，push为放入数据

索引值从左边开始为0，1，2；从右边开始为-1，-2，-3

#### 常用API

* rpush ：从右边插入数据 【rpush name:1:list 001 002 003】
* lpush ：从左边插入数据 【lpush name:1:list 001 002 003】

* rpop ：从右边取出数据并删除数据 【rpop name:1:list 1】
* lpop ：从左边取出数据并删除数据 【lpop name:1:list 1】
* linsert ：插入数据，brfore在...之前，after在...之后；【linsert name:1:list before 001 099】：在001之前插入数据099
* lrem ：删除数据
  * count > 0 : 从表头开始向表尾搜索，移除与 VALUE 相等的元素，数量为 COUNT 。
  * count < 0 : 从表尾开始向表头搜索，移除与 VALUE 相等的元素，数量为 COUNT 的绝对值
  * count = 0 : 移除表中所有与 VALUE 相等的值。
  * 【lrem name:1:list 0 001】
* 参考文档：http://redisdoc.com/list/ltrim.html
* brpop：从右边取出数据并删除数据，如果没有数据，则等待设置的时间，在返回结果。【brpop name:1:list 10】

### Set类型

无序，不允许重复

#### 常用API

 