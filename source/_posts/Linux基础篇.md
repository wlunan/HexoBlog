# Linux基础篇

## 1.1 vi、vim快速入门

![image-20220413100321862](https://imgs-bed-lnnau.oss-cn-hangzhou.aliyuncs.com/lnnau/image-20220413100321862.png)

## 1.2 vi、vim快捷键

**[ ]中的内容是提示信息**

```shell
# 拷贝当前行 光标放置哪一行就拷贝当前行	yy是复制，p是粘贴	yyp就是复制当前行并且粘贴，相当于Ctrl+D
[一般模式] yyp
# 拷贝当前行向下的n行
[一般模式] n yyp
# 在文件中查找某个单词
[命令模式] /关键字+回车 n[查找下一个]
# 设置文件的行号
[命令模式] :set nu
# 取消文件的行号
[命令模式] :set nonu
# 快速到达文件顶端或末端
[一般模式] G[末端]
[一般模式] gg[顶端]
# 移动光标到某一行	n代表移动到第n行
[一般模式] n+shift键+g
# 撤销动作
[一般模式] u
```

## 2. 开机、重启和用户登录注销

```shell
# 立刻进行关机
shutdown -h now
# 1分钟后关机	加提示信息，可以提醒到每个登录该终端的用户
shutdown -h 1
# 现在重启计算机
shutdown -r now
# 关机
halt
# 现在重启计算机
reboot
# 把内存中数据同步到磁盘
sync
```

**注意**

1. 不管重启系统还是关闭系统，首先要运行sync命令，把内存中的数据写到磁盘中
2. 目前的shutdown、reboot、halt等命令均已经在关机前进行了sync，但是不确保所有都有，所以建议执行这些命令前先执行sync

### 2.1 登录注销

```shell
# 切换用户
su 用户名
# 注销用户 在提示符下有效
logout
```

**注意**

1. logout在图形运行级别无效，在运行级别3下有效

## 3. 用户管理

### 3.1 添加、删除和查看用户

```shell
# 添加用户 添加用户后会产生一个和用户名同名的家目录
useradd 用户名
# 添加用户 给新用户指定家目录
useradd -d 目录 用户名
# 修改密码
passwd 用户名
# 删除用户 保留家目录
userdel 用户名
# 删除用户和家目录
userdel -r 用户名
# 查看指定用户信息
id 用户名
# 查看当前用户/登录用户
who am i 或者 whoami 或者 who
```

 **一般情况建议保留家目录** 

### 3.2 用户组

介绍：类似于角色，系统可以对有共性/权限的多个用户进行同一管理

```shell
# 创建用户组
groupadd 组名
# 删除用户组
groupdel 组名
# 创建用户并指定到用户组
useradd -g 组名 用户名
# 修改用户的组
usermod -g 组名 用户名
```

### 3.3 切换用户

介绍：在操作Linux中，如果当前用户的权限不够，可以通过切换到更高权限的用户来执行操作

```shell
# 切换用户
su 用户名 或者 su - 用户名
```

**注意**：

1. 从权限高的用户切到权限低的用户的时候不需要输入密码，反之亦然
2. 当需要返回到原来的用户时，需要输入exit/logout

### 3.4 用户组和相关文件

![image-20220413215641130](https://imgs-bed-lnnau.oss-cn-hangzhou.aliyuncs.com/lnnau/image-20220413215641130.png)

## 4.文件目录类

### 指令

#### pwd

基本语法：pwd（功能描述：显示当前工作目录的绝对路径）

#### ls

基本语法：ls 【选项】【文件或者目录】

常用选项

-a 显示当前目录的所有文件和目录，包括隐藏

-l 以列表的方式显示信息

#### cd

基本语法：cd 【参数】（功能描述：切换到指定目录）

理解：绝对路径和相对路径

cd或者cd ~ ：回到自己的家目录

cd .. 回到当前目录的上一级目录

案例

1. 使用绝对路径切换到root目录	cd /root
2. 使用相对路径到/root目录     cd ../../roor
3. 回到家目录和上一级目录 cd ~ cd ..

#### mkdir

基本语法：mkdir 【选项】【要创建的目录】（用于创建目录）

常用选项：-p（创建多级目录）

案例

1. 创建一个目录 /home/dog	mkdir /home/dog
2. 创建多级目录 /home/animal/tiger  mkdir -p /home/animal/tiger

#### rmdir

基本语法：rmdir 【选项】【要删除的空目录】（用于删除空目录）

案例

删除一个目录 /home/dog 	rmdir /home/dog

细节

rmdir删除的是空目录，如果目录下有内容时无法删除，但是可以通过 rm -rf 强制删除

#### touch

基本语法：touch 【文件名称】（用于创建空文件）

案例：创建一个空文件 hello.txt   	touch hello.txt

#### cp

基本语法：cp 【选项】source dest （用于拷贝文件到指定目录）

常用选项

-r 递归复制整个文件夹

案例

1. 将home/hello.txt 拷贝到home/bbb目录下
2. 递归复制整个文件夹，将/home/bbb整个目录，拷贝到/opt

强制覆盖不提示的方法 /cp

#### mv

mv移动文件或目录或重命名

基本语法

mv oldNameFile newNameFile (**在同一目录下是重命名**)

mv /temp/moveFile /targect	(**不同目录下是移动**)

案例

1. 将/home 下的cat.txt重命名为pig.txt	mv cat.txt pig.txt
2. 将/home下的pig.txt移动到/root     mv pig.txt ../root
3. 移动整个目录，将sunjunhao移动到/home下 mv sunjunhao ../home

#### cat

查看文件内容

基本语法：cat 【选项】要查看的内容

常用选项：-n显示行号

案例：查看/etc/profile 文件内容并显示行号 cat -n /etc/profile

细节：cat只能浏览文件，不能修改文件内容，为了浏览方便，一般带上管道命令|more

cat -n /etc/profile | more 

#### more

more指令是基于vi编辑器的文本过滤器，它以全屏幕的方式按页显示文件内容，more指令中内置若干快捷键

![image-20220420083303584](https://imgs-bed-lnnau.oss-cn-hangzhou.aliyuncs.com/lnnau/image-20220420083303584.png)

案例：采用more查看文件	more /etc/profile

#### less

less在显示文件是根据浏览时加载，不是一次加载完毕，对于浏览大文件很有效

基本语法：less 要查看的文件

![image-20220420083800298](https://imgs-bed-lnnau.oss-cn-hangzhou.aliyuncs.com/lnnau/image-20220420083800298.png)

#### echo

echo 输出内容到控制台

基本语法：echo 【选项】要输出的内容

案例：

使用echo输出环境变量，$PATH $HOSTNAME

使用echo 输出hello world

#### head

显示文件内容的开头部分内容

基本语法

head 文件（查看前十行内容）

head -n 5 文件（查看前五行内容 ，5可修改）

案例：查看/etc/profile前五行内容 head  -n 5 /etc/profile

#### tail

用于输出文件尾部中的内容，默认情况tail显示后十行内容

基本语法：tail 文件

tail -n 5 文件

tail -f 文件 （实时追踪该文档的所有更新）

案例：

查看/etc/profile最后五行	tail -n 5 /etc/profile

实时监控 mydate.txt 	tail -f /home/mydate.txt

#### > >>

">"输出重定向和">>"追加

基本语法

1. ls -l >文件 （将列表的内容写入a.txt（覆盖写））
2. ls -a >>文件 （列表的内容追加到文件aa.txt末尾）
3. cat 文件1 > 文件2（将文件1的内容覆盖到文件2）
4. echo 内容>>文件 （追加）

案例

将/home 目录下的文件列表写入到/home/info.txt中，覆盖写入	在home目录下 ls -l > info.txt

将当前日历信息追加到/home/mycal	cal > mycal

#### ln

软链接也称为符号链接，类似于windows里的快捷方式，主要存放了链接其他文件的路径

基本语法：ln -s【原文件或目录】【软链接名】（给原文件创建一个软链接）

案例

1. 在/home 目录下创建一个软链接myroot 链接到/root目录     ln -s /root /home/myroot
2. 删除软链接 myroot    rm /home/myroot

细节

当使用pwd查看目录是，仍然看到的是软链接所在目录

#### history

查看已经执行过的历史命令，也可以执行历史命令

基本语法：history

案例

显示所有历史命令 history

显示最近的10个指令 history 10

执行编号为5的指令  	!5

## 5.时间日期类

### 1.date显示当前日期

```shell
# 显示当前时间
date
# 显示当前年份
date +%Y
# 显示当前月份
date +%m
# 显示当前是哪一天
date +%d
# 显示年月日时分秒
date +%Y-%d%H:%M:%S
```

案例	记得加上双引号

显示当前时间信息 date

显示当前时间年月日

date "+%Y-%m-%d"

### 2.date设置日期

```shell
date -s 字符串时间
# 案例
# 设置系统当前时间
date -s "2022-4-20 10:00:00"
```

### 3.cal指令

```shell
# 查看日历指令
cal
# 基本语法
cal 【选项】
# 案例
# 显示当前日历 cal
# 显示2022年日历 cal 2022
```

## 6.搜索查找类

### 1.find指令

find指令从指定目录向下递归地遍历其各个子目录，将满足条件的文件或目录显示在终端

![image-20220420095550428](https://imgs-bed-lnnau.oss-cn-hangzhou.aliyuncs.com/lnnau/image-20220420095550428.png)



```shell
# 基本语法
find 【搜索范围】【选项】
# 案例
# 按文件名，根据名称查找/home目录下的hello.txt文件
find /home -name hello.txt
# 按拥有者，查找/opt目录下。用户名为noboby的文件
find /opt -user nobby
# 查找整个Linux系统下大于200M的文件（+n大于 -n小于 n等于，单位k，M，G）
find / -size +200M
```



****
