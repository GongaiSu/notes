# Redis

## 安装

* 安装C 语言

  * **yum** **ins****tall centos-release-scl scl-utils-build**

    **yum install -y devtoolset-8-toolchain**

    **scl enable devtoolset-8 bash**

* 下载redis-6.2.1.tar.gz放/opt目录

* 解压命令：tar -zxvf redis-6.2.1.tar.gz

* 解压完成后进入目录：cd redis-6.2.1

* 在redis-6.2.1目录下再次执行make命令

* 如果没有准备好C语言编译环境，make 会报错—Jemalloc/jemalloc.h：没有那个文件

  * 解决方案：运行make distclean

* 在redis目录下再次执行make命令

* 跳过make test 继续执行: make install

* 安装目录：/usr/local/bin

* 启动(推荐使用后台启动)

  * 备份redis.con

  * 拷贝一份redis.conf到其他目录

    cp /opt/redis-3.2.5/redis.conf /myredis

  * 修改redis.conf(128行)文件将里面的daemonize no 改成 yes，让服务在后台启动

  * redis-server/myredis/redis.conf

* 客户端访问

  * redis-cli -p6379

* 关闭

  * 登陆后：shutdown

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
* select :切换数据库0-15

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

* sadd:将一个或多个值加入到集合 `key` 当中，已经存在于集合的值将被忽略。【sadd s1 gyf】
* Dismember key member[值]:判断`menber`元素是否是集和`key`中的值
* spop key:随机删除一个元素，并返回给前端
* srandmember
  * srandmember key:随机返回一个元素
  * srandmember key count[正整数]:随机返回count个元素，元素不重复，如果count个数大于key中的个数，则返回整个key集合。
  * srandmember key count[负整数]:随机返回count个元素，元素可能重复，如果count个数大于key中的个数，则返回count个元素必有重复元素。
  * srandmember key count[0]:不返回元素
  * 集合为空时则返回nil 或者空数组
* srem key member…[值1,值2…]：删除一个或多个元素，元素不存在则被忽略
* smove key1 key2 member:将member从key1移动到key2中，member从key1中移除，并添加到key2中
  * 如果key1中没有元素member则不进行任何操作，返回0
  * 如果key2不存在，这返回0
  * 如果key2中存在元素member，这是将key1中的member删除。
* scard key:返回集合key中的元素个数，如果key不存在则返回0
* smembers key:返回集合key中的所有元素
* sscan
* sinter [key1,key2...]:返回key1,key2...中共同的部分【交集】，只有一个key，则返回这个key中所有的元素
* sinterstore key [key1,key2...]:将key1,key2...中共同的部分【交集】保存到key中，如果key存在，则将其覆盖
* sunion [key1,key2...]:返回key1和key2...中所有的元素【并集】，只有一个key，则返回这个key中所有的元素
* sunionstore key [key1,key2...]:将key1和key2...中所有的元素【并集】保存到key中，如果key存在，则将其覆盖
* sdiff [key1,key2]:返回key1和key2...中相互之间不同的元素【差集】，只有一个key，则返回这个key中所有的元素
* sdiffstore key [key1,key2...]:将key1和key2...中相互之间不同的元素【差集】保存到key中，如果key存在，则将其覆盖