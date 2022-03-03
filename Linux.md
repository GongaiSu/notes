# Liunx

## Linux目录结构

<span style='color:red'>在Linux中，一切皆文件！！！</span>

1. /bin [<span style='color:red'>常用</span>] (/usr/bin、/usr/local/bin):是Binary的缩写，这个目录存放这最常用使用的命令
2. /sbin (/usr/sbin 、/usr/local/sbin)：s是Super User的意思，这里存放的是系统管理员使用的系统管理程序。
3. /home [<span style='color:red'>常用</span>]：存放普通用户的主目录，在Linux中没一个用户都有一个自己的目录，一般改目录是以用户的账号命名。
4. /root [<span style='color:red'>常用</span>]:该目录为系统管理员，也称作超级权限者的用户主目录
5. /lib 系统开机所需要最基本的动态连接共享库，其作用类似于Windows里的DLL文件。几乎所有的应用程序都需要用到这些共享库
6. /lost+found 这个目录一般情况下是空的，当系统非法关机后，者里存放了一些文件
7. /etc [<span style='color:red'>常用</span>]:所有的系统管理所需要的配置文件和子目录，比如安装mysql数据库的my.conf
8. usr [<span style='color:red'>常用</span>]:这是一个非常重要的目录，用户的很多应用程序和文件都放在这个目录下。
9. /boot [<span style='color:red'>常用</span>]:存放的是启动Linux时使用的一些核心文件，包括一些连接文件及镜像文件
10. /proc 这个目录时一个虚拟目录，它是系统内存的映射，访问这个目录来获取系统信息
11. /srv service缩写，改目录存放一些服务启动这后需要提取的数据
12. /sys 这是linux2.6内核的一个很大的变化。该目录下安装了2.6内核中新出现的一个文件系统sysfs
13. /tmp 这个目录是存放一些临时文件的
14. /dev 类似于windows的设备管理，把所有的硬件用文件的形式存储
15. /media [<span style='color:red'>常用</span>]：linux系统会自动识别一些设备，例如U盘、光驱等等，当识别后，linux会把识别的设备挂载到这个目录下
16. /mnt [<span style='color:red'>常用</span>]:系统提供该目录是为了让用户临时挂载别的文件系统的。我们可以将外部存储挂载在/mnt/上，然后进入该目录就可以查看里面的内容了。
17. /opt 这是给主机额外安装软件所 存放的目录。
18. /usr/local [<span style='color:red'>常用</span>]：这是另外一个给主机额外安装软件所安装的目录，一般是通过编译源码方式安装的程序
19. /var [<span style='color:red'>常用</span>] :这个目录中存放着不断扩充着的东西，习惯将经常被修改的目录放在这个目录下。包括各种日志文件
20. /selinux [security-enhanced linux]: SELinux是一种安全子系统，它能控制程序只能访问特定文件，有三种工作模式，可以自行设置。

## vi和vim的基本介绍

* 正常模式：以正常模式打开一个档案就直接进入一般模式了(默认)。在这个模式中，可以使用【上下左右】按键来移动光标，可以使用【删除字符】或【删除整行】来处理档案内容，也可以使用【复制、粘贴】来处理你的文件数据。
* 插入模式：按下i，I，o，O，a，A，r，R，等任何一个字母之后才会进入编辑模式，一般使用i即可。
* 命令行模式:输入ESC退出插入模式，在输入[:]进入该模式当中，可以使用相关命令，完成读取、存盘、替换、离开vim、显示行号等的动作则是在此模式中达成。

### 常用快捷键

1. 拷贝当前行 yy,拷贝当前行向下的5行 5yy，并粘贴(输入p)。
2. 删除当前行 dd，删除当前行向下的5行 5dd
3. 在文件中查找某个单词[命令模式下'/'关键字，回车 查找，输入n就是查找下一个]
4. 设置文件的行号，取消文件的行号。[命令行下 :set nu 和 :set nonu]
5. 在一般模式下，使用快捷键到该文档的最末行[G]和最首行[gg] 
6. 在一般模式下，使用快捷键[u]进行对之前的操作进行撤销
7. 将光标移动到对应的行号，在一般模式下，使用快捷键[行号 shift+g]

## 关机&重启命令

* shutdown -h now ：立刻进行关机
* shutdown -h 1 ：一分钟后关机
* shutdown -r now 立刻重启
* halt ：关机
* reboot ：重启
* sync : 把内存的数据同步到磁盘

## 用户登录和注销

* 在公司中尽量不要使用root账号登录，root是系统管理员，权限最大，避免操作失误，可以利用普通用户登录，登录之后在使用"su 用户名" 命令来切换成系统管理员身份
* 在提示符下输入logout即可注销用户。在图形运行级别无效，在运行级别3下有效。 

## 用户管理

### 添加用户

*  useradd 用户名 ：默认生成一个和用户名一样的家目录
  * useradd 用户名 -d 目录名 ：指定用户的位置

### 设置密码

* passwd 用户名：给指定用户名设置密码，切记:如果不写用户名则是给当前登录用户修改密码。

### 删除用户

* userdel 用户名 ：删除用户但是保留家目录
* userdel -r 用户名：删除用户和家目录。

### 查询用户

* id 用户名

### 切换用户

* su - 用户名：低等级用户转高等级用户需要输入密码，反正不需要。
* logout：退出当前用户返回上一层用户

### 查看当前用户

* whoami/who am i：查看的是登录用户的信息，不会被切换用户进行影响。

## 用户组

​	类似于角色，系统可以对有共性/权限的多个用户进行统一的管理

### 新增组

* groupadd 组名

### 删除组

* groupdel 组名

### 新增用户时直接加上组

* useradd -g 用户组 用户名 

### 修改用户的组

* usermod -g 用户组 用户名

### 用户和组相关文件

* /etc/passwd文件：用户(user)的配置文件，记录用户的各种信息
  * 每行的含义:用户名:口令:用户标识号:组标识号:注释性描述:主目录:登录Shell
* /etc/shadow文件：口令配置文件
  * 每行含义: 登录名:加密口令:最后一次修改时间:最小时间间隔:最大时间间隔:警告时间:不活动时间:失效时间:标志
* /etc/group文件：组(group)的配置文件，记录Linux包含的组的信息
  * 每行含义:组名:口令:组标识符:组内部用户列表

## 实用指令

### 指定运行级别

* 基本介绍

  * 0：关机

  * 1：单用户【找回密码】

  * 2：多用户状态没有网络服务【一般不用】

  * 3：多用户状态有网络服务

  * 4：系统未使用保留给用户

  * 5：图形界面

  * 6：系统重启

    常用运行级别时3和5，也可以指定默认运行级别

* multi-user.target:analogous to runlevel 3【3级别】

* graphical.target:analogous to runlevel 5【5级别】

* 使用systemctl get-default：查看当前运行级别

* 使用systemctl set-default：multi-user.target/graphical.target：修改默认运行级别

### 帮助指令

* man 获取帮助信息
  * man [命令或配置文件]（获取帮助信息）
* help指令
  * help 命令（获取shell内置命令的帮助信息） 

### 文件目录类

* pwd命令 ：显示当前工作目录的绝对路径
* ls 命令：
  * 基本语法：ls [选项] [目录或者文件]
  * -a：显示当前目录所有的文件和目录，包括隐藏的。
  * -l：以列表的方式显示信息
  * -h：文件的大小显示方式为能看懂
* cd指令：
  * 基本语法：cd [参数] （切换到指定目录）
  * cd ~或者cd ：回到自己的家目录
  * cd .. :返回上一层
* mkdir指令：用于创建目录
  * mkdir [选项] 要创建的目录
  * -p：创建多级目录
* rmdir：用于删除目录
  * rmdir [选项] 要删除的目录
  * 使用细节：rmdir删除的是空目录，如果目录下有内容时无法删除的。一般使用：rm -rf 要删除的目录
* touch指令：创建空文件
  * touch 文件名称
* cp指令： 拷贝文件袋指定目录
  * cp [选项] source dest 
  * -r :递归复制整个文件夹
  * 强制覆盖不提示的方法：\cp
* rm指令：移除文件或目录
  * rm [选项] 要删除的文件或目录
  * -r：递归删除整个文件夹
  * -f：强制删除不提示
* mv指令：移动文件与目录或重命名
  * mv 旧文件名 新文件名 （重命名）
  * mv 被移动文件的位置 移动到的位置 （移动文件）
* cat指令：查看文件内容
  * cat [选项] 要查看的内容
  * -n：显示行号
  * 为了方便浏览，一般会带上 管道命令 | more 
* more 指令：是一个基于vi编辑器的文本过滤器，它以全屏幕的方法按页显示文本文件的内容。more指令中内置了若干快捷键
  * more 要查看的文件
    * 按空格：向下翻一页；
    * 按回车：向下翻一行
    * 按q：离开more，不再显示该文件内容
    * 按Ctrl+F：向上滚动一屏
    * 按Ctrl+B：返回上一屏
    * =：输出当前行的行号
    * :f ：输出文件名和当前行的行号
* less指令：用来分屏查看文件内容，它的功能与more指令类似，但是比more指令更加的强大，支持各种显示终端，less指令在显示文件内容时，并不是以一次将整个文件加载之后才显示，而是根据显示需要加载的内容，对于显示大型文件具有较高的效率
  * less 查询的文件
    * 按空格：向下翻动一页；
    * 按[pagedown]：向下翻动一页；
    * 按[pageup]：向上翻动一页；
    * /字符串 ：向下搜寻【字符串】的功能，按n向下查找；按N向上查找
    * ?字符串：向上搜寻【字符串】的功能，按n向上查找；按N向下查找
    * q：退出less
* echo指令：输出内容到控制台
  * echo [选项] [输出的内容]
* head指令 ：用于显示文件的开头部分内容，默认情况下head指令显示文件的前10行内容
  * head 文件：查看文件的前10行内容
  * head -n 5 文件 ：显示文件的前5行内容，5可以时任意行数
* tail指令：用户输出文件尾部的内容，默认情况下tail指令显示文件的后10行内容
  * tail 文件 ：显示文件尾10行内容
  * tail -n 5 文件 ：显示文件尾5行内容，5可以是任意行数
  * tail -f 文件：实时追踪该文件的所有更新
* '>'指令 和‘>>’指令 ：'>'覆盖；'>>'追加
  * ls -l > 文件：将列表的内容写入到文件中，覆盖写
  * ls -al >> 文件：将列表的内容追加到文件的末尾
  * cat 文件1 > 文件2 ：将文件1的内容覆盖文件2中
  * echo “内容”  >> 文件 ：将引号中的内容追加到文件中
* ln指令：软链接也称为符号链接，类似于window里的快捷键方式，主要存放了链接文件的路径
  * ln -s [原文件或目录] [软链接名]（给原文件创建一个软链接）
  * 实例
    * Q:在/home目录下创建一个软链接，链接到/root目录
    * A:ls -n /root /home/myroot
    * Q:删除软链接myroot
    * A: rm -rf /home/myroot
* history指令：查看已经执行过历史命令，也可以执行历史命令
  * history (查看已经执行过的历史命令)
  * 执行之前的命令在命令编号前添加【!】
  * 实例
    * Q:显示所有的历史命令
    * A：history
    * Q:显示最近使用过的10个指令
    * A：history 10
    * Q:执行历史编号为5的指令
    * A：!5

### 时间日期类

* date指令：显示当前日期
  * date (显示当前时间)
  * date + %Y  (显示当前年份)
  * date + %m (显示当前月份)
  * date + %d (显示当前是哪一天）
  * date "+ %Y-%m-%d %H:%M:%S" (显示当前年月日时分秒)
  * date -s 字符串时间（设置日期）
  * 实例
    * Q：显示当前时间信息
    * A：date
    * Q：显示当前时间年月日
    * A：date "+%Y-%m-%d"
    * Q：显示当前时间年月日时分秒
    * A：date "+%Y-%m-%d %H:%M:%S"
* cal指令：查看日历指令 
  * cal [选项] （不加选项，显示本月日历）
  * 实例
    * Q：显示当前日历
    * A：cal
    * Q：显示2020年日历
    * A：cal 2020

### 搜索查询类

* find指令：将从指令目录向下递归地遍历其各个子目录，将满足条件的文件或目录显示在终端。
  * find [搜索范围] [选项]
    * -name<查询方法>：按照指定的文件名查找模式查找文件
    * -user<用户名>：查找属于指定用户名所有文件
    * -size<文件大小>：按照指定的文件大小查找文件
  * 案例
    * Q：按文件名：根据名称查找/home目录下的hello.txt文件
    * A：find /home -name hello.txt
    * Q：按拥有者：查找/opt目录下，用户名称为nobody的文件
    * A：find /opt -user nobody
    * Q：查找整个linux系统下大于200M的文件（+n大于 -n小于 n等于）
    * A：find / -size 200M
* locate指令：可以快速定位文件路径
  * locate 搜索文件
  * 说明：由于locate指令基于数据库进行查询，所以第一次执行前，必须使用updatedb指令创建locate数据库
  * 案例
    * Q：使用locate指令快速定位hello.txt文件目录
    * A：现进行updatedb，在执行locate hello.txt(如果显示未找到命令：先执行：yum install mlocate；安装locate)
* which指令：查看某个命令在那个目录下
  * which [选项]
  * 案例
    * Q：查看ls命令在那个目录下
    * A：which ls
* grep指令和管道符号|：grep锅炉查询，管道符，“|”，表示将前一个命令的处理结果输出传递给后面的命令处理。
  * grep [选项] 查找内容 原文件
    * -n：显示匹配行及行号
    * -i：忽略字母大小写
    * **-a 或 --text** : 不要忽略二进制的数据。
    * **-A<显示行数> 或 --after-context=<显示行数>** : 除了显示符合范本样式的那一列之外，并显示该行之后的内容。
    * **-b 或 --byte-offset** : 在显示符合样式的那一行之前，标示出该行第一个字符的编号。
    * **-B<显示行数> 或 --before-context=<显示行数>** : 除了显示符合样式的那一行之外，并显示该行之前的内容。
    * **-c 或 --count** : 计算符合样式的列数。
    * **-C<显示行数> 或 --context=<显示行数>或-<显示行数>** : 除了显示符合样式的那一行之外，并显示该行之前后的内容。
    * **-d <动作> 或 --directories=<动作>** : 当指定要查找的是目录而非文件时，必须使用这项参数，否则grep指令将回报信息并停止动作。
    * **-e<范本样式> 或 --regexp=<范本样式>** : 指定字符串做为查找文件内容的样式。
    * **-E 或 --extended-regexp** : 将样式为延伸的正则表达式来使用。
    * **-f<规则文件> 或 --file=<规则文件>** : 指定规则文件，其内容含有一个或多个规则样式，让grep查找符合规则条件的文件内容，格式为每行一个规则样式。
    * **-F 或 --fixed-regexp** : 将样式视为固定字符串的列表。
    * **-G 或 --basic-regexp** : 将样式视为普通的表示法来使用。
    * **-h 或 --no-filename** : 在显示符合样式的那一行之前，不标示该行所属的文件名称。
    * **-H 或 --with-filename** : 在显示符合样式的那一行之前，表示该行所属的文件名称。
    * **-i 或 --ignore-case** : 忽略字符大小写的差别。
    * **-l 或 --file-with-matches** : 列出文件内容符合指定的样式的文件名称。
    * **-L 或 --files-without-match** : 列出文件内容不符合指定的样式的文件名称。
    * **-n 或 --line-number** : 在显示符合样式的那一行之前，标示出该行的列数编号。
    * **-o 或 --only-matching** : 只显示匹配PATTERN 部分。
    * **-q 或 --quiet或--silent** : 不显示任何信息。
    * **-r 或 --recursive** : 此参数的效果和指定"-d recurse"参数相同。
    * **-s 或 --no-messages** : 不显示错误信息。
    * **-v 或 --invert-match** : 显示不包含匹配文本的所有行。
    * **-V 或 --version** : 显示版本信息。
    * **-w 或 --word-regexp** : 只显示全字符合的列。
    * **-x --line-regexp** : 只显示全列符合的列。
    * **-y** : 此参数的效果和指定"-i"参数相同
  * 实例
    * Q：请在hello.txt文件中，查找“yes”所在行，并且显示行号。
    * A1：grep -ni "yes" hello.txt
    * A2：cat hello.txt | grep -n "yes"

### 压缩和解压类

* gzip/gunzip指令：gzip用于压缩文件，gunzip用于解压的
  * gzip 文件（压缩文件，只能将文件压缩为*.gz文件）
  * gunzip 文件.gz（解压缩文件命令）
  * 实例
    * Q：将/home下的hello.txt文件进行压缩
    * A：gzip /home/hello.txt
    * Q：将/home下的hello.txt.gz文件进行解压缩
    * A：gunzip /home/hello.txt.gz
* zip/unzip指令：zip用于压缩文件，unzip用于解压
  * zip [选项] XXX.zip 将要压缩的文件（压缩文件和目录的命令）
    * -r：递归压缩，即压缩目录
  * unzip [选项] XXX.zip (解压缩文件)
    * -d<目录> ：指定解压后文件存放目录
  * 实例
    * Q：将/home下的所有文件进行压缩成myhome.zip
    * A：zip -r myhome.zip /home
    * Q：将myhome.zip 解压到/opt/tmp目录下
    * A：unzip -d /opt/tmp myhome.zip
* tar指令：打包命令
  * tar [选项] XXX.tar.gz 打包的内容 (打包目录，压缩后的文件格式.tar.gz)
    * -c：产生.tar打包文件
    * -v：显示详细信息
    * -f：指定压缩后的文件名
    * -z：打包同时压缩
    * -x：解包.tar文件
  * 实例
    * Q：压缩多个文件，将/home/pig.txt和/home/cat.txt压缩成pc.tar.gz
    * A：tar -zcvf pc.tar.gz /home/pig.txt /home/cat.txt
    * Q：将/home的文件夹压缩成myhome.tar.gz
    * A：tar -zcvf home.tar.gz /home
    * Q：将pc.tar.gz解压到当前文件夹
    * A：tar -zxvf pc.tar.gz
    * Q：将myhome.tar.gz 解压到/opt/tmp2目录下
    * A：tar -zxvf  myhome.tar.gz -C /opt/tmp2

## 权限指令

### 文件/目录 所有者

* chown指令：修改文件所有者
  * chown 用户名 文件名

### 组的创建

* groupadd指令：创建组
  * groupadd 组名
* chgrp指令 ：修改文件所在的组
  * chgrp 组名 文件名 

 