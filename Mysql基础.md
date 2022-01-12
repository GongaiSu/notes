# Mysql基础

# SQL的分类

## DDL（数据定义语言）

* create\atler\drop\rename\truncate

## DML（数据操作语言）

* insert\delete\update\select

## DCL（数据控制语言）

* commit\rollback\savepoint\grant\revoke

# SELECT基础语句

* AS:取别名；例： SELECT  department_id AS dept_id  FROM employees
  * AS可以不写。
  * 别名可以加双引号，可以不加。但是在别名中有空格必须加上引号
    * 例：SELECT  department_id AS "dept id"  FROM employees
* DISTINCK：去除重复的值。例： SELECT DISTINCT department_id  FROM employees
* 空值参与计算结果一定为空，null不能等同于0，' ' ’null‘
* 着重号：``字段名或表名和保留字重复，需要加着重号；
* 显示表结构：describe 表名；DESC 表名也行。
* 过滤数据：where  和FROM挨着，where不能使用别名进行判断 

## 运算符

### 比较运算符

* IS NULL:判断为null
* IS NOT NULL:判断不为null
* ISNULL:判断为null，ISNULL(查询的字段)
* LEAST：最小，判断那个值最小
* GREATEST：最大，判断那个值最大
* BETWEEN 条件1  AND 条件2：查询在条件1和条件2之间的内容；BETWEEN前添加NOT取反。
* IN：查询在IN后括号中内容
* NOT IN：查询不在IN括号后的内容
* LIKE ：模糊查询，
  * %：表示不确定个数的字符
  * _：表示一个不确定的字符
* REGEXP ：正则表达式

### 逻辑运算符

* ！或NOT：取反
* AND或&&：且运算
* ||或OR ：或运算
* XOR：异或

### 位运算符

* & ：按位与
* |：按位或
* ^：按位异或
* ~：取反
* “>>” ：右移
* "<<"：左移

## 排序与分页

### 排序

* ORDER BY ：在WHERE后面
  * DESC 降序
  * ASC：升序(默认)

### 二级排序

* 在排序完后再对需要排序的字段进行排序

### 分页

* LIMIT ：
  * 每页显示pageSize条记录，此时显示第pageNo页；
  * 公式：LIMIT  (pageNo-1)*pageSize,pageSize;

## 多表查询

* n张表进行关联时，至少需要n-1个关联条件
* SQL92语法：
  * 92语法实现外连接：mysql不支持SQL92语法的外连接
* SQL99语法：
  * 使用JOIN…ON实现内连接
  * 使用LEFT  JOIN … ON实现左外连接
  * 使用RIGHT JOIN … ON实现左外连接
  * Mysql不支持FULL JOIN的满外连接
  * 新特性
    * 自然连接：NATURAL JOIN 会自动查询两张表中所有相同的字段
    * USING连接：两张表中关联字段名字相同
* UNION：UNION 操作符用于合并两个或多个 SELECT 语句的结果集。请注意，UNION 内部的 SELECT 语句必须拥有相同数量的列。列也必须拥有相似的数据类型。同时，每条 SELECT 语句中的列的顺序必须相同。（会取出重复的数据）
* UNION ALL UNION 操作符用于合并两个或多个 SELECT 语句的结果集，（不会去除重复的数据）

## 单行函数

### 数值函数

* 基本函数
  
  | 函数                | 用法                                                         |
  | ------------------- | ------------------------------------------------------------ |
  | ABS(x)              | 返回x的绝对值                                                |
  | SIGN(X)             | 返回X的符号。正数返回1，负数返回-1，0返回0                   |
  | PI()                | 返回圆周率的值                                               |
  | CEIL(x)，CEILING(x) | 返回大于或等于某个值的最小整数                               |
  | FLOOR(x)            | 返回小于或等于某个值的最大整数                               |
  | LEAST(e1,e2,e3…)    | 返回列表中的最小值                                           |
  | GREATEST(e1,e2,e3…) | 返回列表中的最大值                                           |
  | MOD(x,y)            | 返回X除以Y后的余数                                           |
  | RAND()              | 返回0~1的随机值                                              |
  | RAND(x)             | 返回0~1的随机值，其中x的值用作种子值，相同的X值会产生相同的随机数 |
  | ROUND(x)            | 返回一个对x的值进行四舍五入后，最接近于X的整数               |
  | ROUND(x,y)          | 返回一个对x的值进行四舍五入后最接近X的值，并保留到小数点后面Y位 |
  | TRUNCATE(x,y)       | 返回数字x截断为y位小数的结果                                 |
  | SQRT(x)             | 返回x的平方根。当X的值为负数时，返回NULL                     |
  
  

### 字符串函数

| 函数                              | 用法                                                         |
| --------------------------------- | ------------------------------------------------------------ |
| ASCII(S)                          | 返回字符串S中的第一个字符的ASCII码值                         |
| CHAR_LENGTH(s)                    | 返回字符串s的字符数。作用与CHARACTER_LENGTH(s)相同           |
| LENGTH(s)                         | 返回字符串s的字节数，和字符集有关                            |
| CONCAT(s1,s2,......,sn)           | 连接s1,s2,......,sn为一个字符串                              |
| CONCAT_WS(x, s1,s2,......,sn)     | 同CONCAT(s1,s2,...)函数，但是每个字符串之间要加上x           |
| INSERT(str, idx, len, replacestr) | 将字符串str从第idx位置开始，len个字符长的子串替换为字符串replacestr |
| REPLACE(str, a, b)                | 用字符串b替换字符串str中所有出现的字符串a                    |
| UPPER(s) 或 UCASE(s)              | 将字符串s的所有字母转成大写字母                              |
| LOWER(s)  或LCASE(s)              | 将字符串s的所有字母转成小写字母                              |
| LEFT(str,n)                       | 返回字符串str最左边的n个字符                                 |
| RIGHT(str,n)                      | 返回字符串str最右边的n个字符                                 |
| LPAD(str, len, pad)               | 用字符串pad对str最左边进行填充，直到str的长度为len个字符     |
| RPAD(str ,len, pad)               | 用字符串pad对str最右边进行填充，直到str的长度为len个字符     |
| LTRIM(s)                          | 去掉字符串s左侧的空格                                        |
| RTRIM(s)                          | 去掉字符串s右侧的空格                                        |
| TRIM(s)                           | 去掉字符串s开始与结尾的空格                                  |
| TRIM(s1 FROM s)                   | 去掉字符串s开始与结尾的s1                                    |
| TRIM(LEADING s1 FROM s)           | 去掉字符串s开始处的s1                                        |
| TRIM(TRAILING s1 FROM s)          | 去掉字符串s结尾处的s1                                        |
| REPEAT(str, n)                    | 返回str重复n次的结果                                         |
| SPACE(n)                          | 返回n个空格                                                  |
| STRCMP(s1,s2)                     | 比较字符串s1,s2的ASCII码值的大小                             |
| SUBSTR(s,index,len)               | 返回从字符串s的index位置其len个字符，作用与SUBSTRING(s,n,len)、MID(s,n,len)相同 |
| LOCATE(substr,str)                | 返回字符串substr在字符串str中首次出现的位置，作用于POSITION(substr IN str)、INSTR(str,substr)相同。未找到，返回0 |
| ELT(m,s1,s2,…,sn)                 | 返回指定位置的字符串，如果m=1，则返回s1，如果m=2，则返回s2，如果m=n，则返回sn |
| FIELD(s,s1,s2,…,sn)               | 返回字符串s在字符串列表中第一次出现的位置                    |
| FIND_IN_SET(s1,s2)                | 返回字符串s1在字符串s2中出现的位置。其中，字符串s2是一个以逗号分隔的字符串 |
| REVERSE(s)                        | 返回s反转后的字符串                                          |
| NULLIF(value1,value2)             | 比较两个字符串，如果value1与value2相等，则返回NULL，否则返回value1 |

### 时间函数

#### 获取日期、时间

| 函数                      | 用法                                 |
| ------------------------- | ------------------------------------ |
| CURDATE()，CURRENT_DATE() | 获取当前时间，只包含年月日           |
| CURTIME()                 | 获取当前时间，包含年月日,时分明      |
| now(),SYSDATE()           | 获取系统时间                         |
| UTC_DATE()                | 获取世界标准时间，只包含年月日       |
| UTC_TIME()                | 获取世界标准时间，包含年月日，时分秒 |

#### 日期与时间戳的转换

| 函数                     | 用法                                                         |
| ------------------------ | ------------------------------------------------------------ |
| UNIX_TIMESTAMP()         | 以UNIX时间戳的形式返回当前时间。SELECT UNIX_TIMESTAMP() ->1634348884 |
| UNIX_TIMESTAMP(date)     | 将时间date以UNIX时间戳的形式返回。                           |
| FROM_UNIXTIME(timestamp) | 将UNIX时间戳的时间转换为普通格式的时间                       |

#### 获取月份、星期、星期数、天数等函数

| 函数                                     | 用法                                            |
| ---------------------------------------- | ----------------------------------------------- |
| YEAR(date) / MONTH(date) / DAY(date)     | 返回具体的日期值                                |
| HOUR(time) / MINUTE(time) / SECOND(time) | 返回具体的时间值                                |
| MONTHNAME(date)                          | 返回月份：January，...                          |
| DAYNAME(date)                            | 返回星期几：MONDAY，TUESDAY.....SUNDAY          |
| WEEKDAY(date)                            | 返回周几，注意，周1是0，周2是1，。。。周日是6   |
| QUARTER(date)                            | 返回日期对应的季度，范围为1～4                  |
| WEEK(date) ， WEEKOFYEAR(date)           | 返回一年中的第几周                              |
| DAYOFYEAR(date)                          | 返回日期是一年中的第几天                        |
| DAYOFMONTH(date)                         | 返回日期位于所在月份的第几天                    |
| DAYOFWEEK(date)                          | 返回周几，注意：周日是1，周一是2，。。。周六是7 |

#### 日期的操作函数

| 函数                    | 用法                                       |
| ----------------------- | ------------------------------------------ |
| EXTRACT(type FROM date) | 返回指定日期中特定的部分，type指定返回的值 |

EXTRACT(type FROM date)函数中type的取值与含义：

![image-20211012142639469](C:\Users\gyf\Desktop\笔记\01_课件\images\image-20211012142639469.png)

![image-20211012142746444](C:\Users\gyf\Desktop\笔记\01_课件\images\image-20211012142746444.png)

#### 时间和秒钟转换的函数


| 函数                 | 用法                                                         |
| -------------------- | ------------------------------------------------------------ |
| TIME_TO_SEC(time)    | 将 time 转化为秒并返回结果值。转化的公式为：`小时*3600+分钟*60+秒` |
| SEC_TO_TIME(seconds) | 将 seconds 描述转化为包含小时、分钟和秒的时间                |

#### 计算日期和时间的函数

| 函数                                                         | 用法                                           |
| ------------------------------------------------------------ | ---------------------------------------------- |
| DATE_ADD(datetime, INTERVAL  expr type)，ADDDATE(date,INTERVAL expr type) | 返回与给定日期时间相差INTERVAL时间段的日期时间 |
| DATE_SUB(date,INTERVAL expr type)，SUBDATE(date,INTERVAL expr type) | 返回与date相差INTERVAL时间间隔的日期           |

上述函数中type的取值：

![image-20220112153327144](C:\Users\gyf\AppData\Roaming\Typora\typora-user-images\image-20220112153327144.png)

| 函数                         | 用法                                                         |
| ---------------------------- | ------------------------------------------------------------ |
| ADDTIME(time1,time2)         | 返回time1加上time2的时间。当time2为一个数字时，代表的是`秒`，可以为负数 |
| SUBTIME(time1,time2)         | 返回time1减去time2后的时间。当time2为一个数字时，代表的是`秒`，可以为负数 |
| DATEDIFF(date1,date2)        | 返回date1 - date2的日期间隔天数                              |
| TIMEDIFF(time1, time2)       | 返回time1 - time2的时间间隔                                  |
| FROM_DAYS(N)                 | 返回从0000年1月1日起，N天以后的日期                          |
| TO_DAYS(date)                | 返回日期date距离0000年1月1日的天数                           |
| LAST_DAY(date)               | 返回date所在月份的最后一天的日期                             |
| MAKEDATE(year,n)             | 针对给定年份与所在年份中的天数返回一个日期                   |
| MAKETIME(hour,minute,second) | 将给定的小时、分钟和秒组合成时间并返回                       |
| PERIOD_ADD(time,n)           | 返回time加上n后的时间                                        |

#### 日期的格式化和解析

| 函数                              | 用法                                       |
| --------------------------------- | ------------------------------------------ |
| DATE_FORMAT(date,fmt)             | 按照字符串fmt格式化日期date值              |
| TIME_FORMAT(time,fmt)             | 按照字符串fmt格式化时间time值              |
| GET_FORMAT(date_type,format_type) | 返回日期字符串的显示格式                   |
| STR_TO_DATE(str, fmt)             | 按照字符串fmt对str进行解析，解析为一个日期 |

上述`非GET_FORMAT`函数中fmt参数常用的格式符：

| 格式符 | 说明                                                        | 格式符 | 说明                                                        |
| ------ | ----------------------------------------------------------- | ------ | ----------------------------------------------------------- |
| %Y     | 4位数字表示年份                                             | %y     | 表示两位数字表示年份                                        |
| %M     | 月名表示月份（January,....）                                | %m     | 两位数字表示月份（01,02,03。。。）                          |
| %b     | 缩写的月名（Jan.，Feb.，....）                              | %c     | 数字表示月份（1,2,3,...）                                   |
| %D     | 英文后缀表示月中的天数（1st,2nd,3rd,...）                   | %d     | 两位数字表示月中的天数(01,02...)                            |
| %e     | 数字形式表示月中的天数（1,2,3,4,5.....）                    |        |                                                             |
| %H     | 两位数字表示小数，24小时制（01,02..）                       | %h和%I | 两位数字表示小时，12小时制（01,02..）                       |
| %k     | 数字形式的小时，24小时制(1,2,3)                             | %l     | 数字形式表示小时，12小时制（1,2,3,4....）                   |
| %i     | 两位数字表示分钟（00,01,02）                                | %S和%s | 两位数字表示秒(00,01,02...)                                 |
| %W     | 一周中的星期名称（Sunday...）                               | %a     | 一周中的星期缩写（Sun.，Mon.,Tues.，..）                    |
| %w     | 以数字表示周中的天数(0=Sunday,1=Monday....)                 |        |                                                             |
| %j     | 以3位数字表示年中的天数(001,002...)                         | %U     | 以数字表示年中的第几周，（1,2,3。。）其中Sunday为周中第一天 |
| %u     | 以数字表示年中的第几周，（1,2,3。。）其中Monday为周中第一天 |        |                                                             |
| %T     | 24小时制                                                    | %r     | 12小时制                                                    |
| %p     | AM或PM                                                      | %%     | 表示%                                                       |

GET_FORMAT函数中date_type和format_type参数取值如下：

![image-20220112153440076](C:\Users\gyf\AppData\Roaming\Typora\typora-user-images\image-20220112153440076.png)

### 流程控制函数

流程处理函数可以根据不同的条件，执行不同的处理流程，可以在SQL语句中实现不同的条件选择。MySQL中的流程处理函数主要包括IF()、IFNULL()和CASE()函数。

| 函数                                                         | 用法                                            |
| ------------------------------------------------------------ | ----------------------------------------------- |
| IF(value,value1,value2)                                      | 如果value的值为TRUE，返回value1，否则返回value2 |
| IFNULL(value1, value2)                                       | 如果value1不为NULL，返回value1，否则返回value2  |
| CASE WHEN 条件1 THEN 结果1 WHEN 条件2 THEN 结果2 .... [ELSE resultn] END | 相当于Java的if...else if...else...              |
| CASE  expr WHEN 常量值1 THEN 值1 WHEN 常量值1 THEN 值1 .... [ELSE 值n] END | 相当于Java的switch...case...                    |

## 聚合函数

### 常见的聚合函数

* AVG：平均值，SUM：和值：只适用于数值类型
* MAX：最大值，MIN：最小值：使用与数值类型，字符类型，日期类型的字段
* COUNT：计算指定字段在查询结构中出现的个数，查询时出现的个数不包含空值
* 会自动过滤空值

### GROUP BY

* 分组，安装BY后面的字段进行分组，可以对多字段分组，SELECT出现的非组函数的字段必须声明在GROUP BY中，而GROUP BY出现的字段可以不用出现在SELECT中。
* 出现位置为from，where后面，order by，limit前面。

### HAVING

* 过滤条件中使用了聚合函数，则必须使用HAVING来替换WHERE。
* HAVING必须放在GROUP BY后面

## 子查询

* 想到与嵌套查询，使用内查询的结果，对外查询进行过滤

  
