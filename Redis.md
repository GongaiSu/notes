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
* sismember key member[值]:判断`menber`元素是否是集和`key`中的值
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

### Zset类型

#### 常用API

* zadd key score member:将一个带有分数的member添加到key中，如果member存在score不同则会修改分数前端返回0；score可以为整型或双精度浮点型
* zscore key member:返回当前key中member的分数，如果key或者member不存在则返回nil
* zincrby key 数值 member:给key中的member值的分数增加对应的数值的量，如果数值为负数，则是减少。
* zcard key:返回key中的值的个数
* zcount key min max:返回key中分数在min到max之间值的个数(包含分数最大值和最小值)
* zrange key start stop:返回key中从start到stop的值，值的顺序是按照分数的大小递增(从小到大)；
  * 下标参数 `start` 和 `stop` 都以 `0` 为底，也就是说，以 `0` 表示有序集第一个成员，以 `1` 表示有序集第二个成员，以此类推。 你也可以使用负数下标，以 `-1` 表示最后一个成员， `-2` 表示倒数第二个成员，以此类推。
  * 分数相同，member也按照从小到大排序
* zrevrange key start stop:返回key中从start到stop的值，值的顺序是按照分数的大小递减(从大到小)；分数相同，member也按照从大到小排序
* zrangebysocre key min max: 返回key中分数在min到max之间值(包含分数max和min)，值的顺序是按照分数的大小递增(从小到大)；
  * `min` 和 `max` 可以是 `-inf` 和 `+inf`表示无穷小到无穷大。
  * 区间的取值使用[闭区间](http://zh.wikipedia.org/wiki/區間) (小于等于或大于等于)，你也可以通过给参数前增加 `(` 符号来使用可选的[开区间](http://zh.wikipedia.org/wiki/區間) (小于或大于)。
  * `WITHSCORES`:在最后添加这个显示分数是多少
* zrevrangebyscore key max min: 返回key中分数在max到min之间值(包含分数max和min)，值的顺序是按照分数的大小递减(从大到小)
  * `min` 和 `max` 可以是 `-inf` 和 `+inf`表示无穷小到无穷大。
  * 区间的取值使用[闭区间](http://zh.wikipedia.org/wiki/區間) (小于等于或大于等于)，你也可以通过给参数前增加 `(` 符号来使用可选的[开区间](http://zh.wikipedia.org/wiki/區間) (小于或大于)。
  * `WITHSCORES`:在最后添加这个显示分数是多少
* zrank key member:返回key中member的排名，按照成员的分数值进行递增(从小到大)；返回的值是以0开始。
* zrevrank key member:返回key中member的排名，按照成员的分数值进行递减(从大到小)；返回的值是以0开始
* zrem key member:删除key中的member，返回删除member的数量
* zremrangebyrank key start stop:删除key中从start到stop的值(包含start，stop)
  * 下标参数 `start` 和 `stop` 都以 `0` 为底，也就是说，以 `0` 表示有序集第一个成员，以 `1` 表示有序集第二个成员，以此类推。 你也可以使用负数下标，以 `-1` 表示最后一个成员， `-2` 表示倒数第二个成员，以此类推。
* zremrangebyscore key min max:删除key中分数在min到max之间值(包含分数max和min)

### 发布与订阅

* subscribe channel:订阅频道，可以订阅多个频道。
* psubscribe pattern:订阅模式，模式以`*`作为匹配符，例如it开头的模式，如it.news。it.*则是订阅所有以it.开头的所有频道。
* publish channel or pattern member;发布消息member给channel频道或pattern模式的频道。
* 退订频道需要查询一下。

### Bitmaps类型

* 本身不是有一种数据类型，实际上就是字符串，但是它可以对字符串的位进行操作。

#### 常用API

* setbit key offset value:
  * 对 `key` 所储存的字符串值，设置或清除指定偏移量上的位(bit)。
  * 位的设置或清除取决于 `value` 参数，可以是 `0` 也可以是 `1` 。
  * 当 `key` 不存在时，自动生成一个新的字符串值。
  * 字符串会进行伸展(grown)以确保它可以将 `value` 保存在指定的偏移量上。当字符串值进行伸展时，空白位置以 `0` 填充。
  * `offset` 参数必须大于或等于 `0` ，小于 2^32 (bit 映射被限制在 512 MB 之内)。
* setbit key offset:
  * 对 `key` 所储存的字符串值，获取指定偏移量上的位(bit)。
  * 当 `offset` 比字符串值的长度大，或者 `key` 不存在时，返回 `0` 。
  * 字符串值指定偏移量上的位(bit)。
* bitcount key:返回可以偏移量上为`1`的个数。
* bitpos key bit :返回位图中第一个值为 `bit` 的二进制位的位置。
* BITOP operation destkey key [key …]:对一个或多个保存二进制位的字符串 `key` 进行位元操作，并将结果保存到 `destkey` 上。
  * `operation` 可以是 `AND` 、 `OR` 、 `NOT` 、 `XOR` 这四种操作中的任意一种：
    - `BITOP AND destkey key [key ...]` ，对一个或多个 `key` 求逻辑并，并将结果保存到 `destkey` 。
    - `BITOP OR destkey key [key ...]` ，对一个或多个 `key` 求逻辑或，并将结果保存到 `destkey` 。
    - `BITOP XOR destkey key [key ...]` ，对一个或多个 `key` 求逻辑异或，并将结果保存到 `destkey` 。
    - `BITOP NOT destkey key` ，对给定 `key` 求逻辑非，并将结果保存到 `destkey` 。
  * 除了 `NOT` 操作之外，其他操作都可以接受一个或多个 `key` 作为输入。

### Hyperloglog类型

* 基数:不重复的元素

#### 常用API

* pfadd key element：给key中添加值，重复值不添加返回0
* pfcount key:返回key中的元素个数
* pfmerge destkey key1 key2:将key1和key2中的值合并添加到destkey中。

### Geospatial(地理位置)

#### 常用API

* GEOADD key longitude latitude membe:
  * longitude :经度，有效的经度介于 -180 度至 180 度之间。
  * latitude ：维度，有效的纬度介于 -85.05112878 度至 85.05112878 度之间
  * 给key中添加一个带有经度和维度的地理位置
* GEOPOS key member:获取key中member的经纬度
* GEODIST key member1 member2 unit :返回两个位置之间的距离
  * unit可选
    * `m` 表示单位为米。
    * `km` 表示单位为千米。
    * `mi` 表示单位为英里。
    * `ft` 表示单位为英尺

### 事务

* redis的事务是将多个命令进行串行化，防止其他命令进行插队。

### 基本操作

* multi:开启事务，将多个命令放到一个队列中，如果入队的命令有错则之前所有的命令都无法执行。
* exec:执行所有的操作。如果在multi入队时的命令没有报错，在执行时报错，则不会影响其他正确的命令执行，当前错误的命令这报错。
* discard:取消事务，放弃执行事务块中的所有命令

### 三特征

* 单独的隔离操作：事务的所有命令都会序列化，按顺序执行。事务的执行过程中不会被其他客户端发送的命令打断。
* 没有隔离级别的概念:队列中的命令没有提交前不会实际被执行，因为事务提交前任何指令都不会被实际执行。
* 不保证原子性：事务中如果有一条命令执行失败，其他的命令仍然会被执行，没回回滚。

### 事务冲突

* 悲观锁：在执行每一步操作时都进行加锁。
* 乐观锁：每次执行的时候会判断一下在此期间别人有没有去更新这个数据，可以使用版本号等机制。
  * 使用watch key来进行对key的监控的变化
  * 使用unwatch来进行取消所有key的监控

