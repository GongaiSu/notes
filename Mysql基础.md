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

