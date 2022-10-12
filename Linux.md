# Linux

小坑：喜欢按ctrl+s的你在linux会触发终端的滚动锁定

按ctrl+q可以解除锁定

查看内存占用前10 ：ps aux | head -1;ps aux |grep -v PID |sort -rn -k +4 | head -10

#### Linux如何运行和停止jar包

nohup java -jar Myboke.jar &

 //意思是不挂断运行命令,当账户退出或终端关闭时,程序仍然运行  

ctrl + c 退出控制面板，系统不停止

查看当前应用所占用端口：

netstat  -nlp|grep 8089   //8089是系统启动访问的端口， 由此可得到9578 是java运行的端口，

![img](https://img2018.cnblogs.com/blog/827510/201911/827510-20191122163414722-1651229583.png)

 关闭系统：

kill 9578  //9578是上面查询出来的端口

mkdir -p logs  创建日志文件夹

cd logs 进入日志文件夹

touch 1.log 创建日志文件

cd .. 返回上层目录

nohup java -jar Myboke.jar >logs/myboke.log  启动服务 xxxx替换成你的jar包名称 指定日志输出到1.log里面

上面的是阻塞线程 如果需要后台运行 在命令最后面加个 & 号 

cd logs 进入目录文件夹

tail -f -n 1000 1.log 查看你的日志 看是否启动成功了

ps -ef|grep Myboke.jar 也可以通过进程查看你的jar包运行情况



#### 硬件使用情况

```bash
#看cpu
lscpu
#查看CPU的详细信息
cat /proc/cpuinfo
#查看内存使用情况
free -m
#查看内存使用详细信息
cat /proc/meminfo
#查看硬盘和分区情况
lsblk
#查看硬盘和分区详细情况
fdisk -l
#查看系统版本
cat /etc/redhat-release
```





使用：Xshell

核心：对/root下文件的读，写以及权限控制

所需下载工具:XShell,Xftp,宝塔，CentOS.....

配置好端口

![image-20220121210542842](C:\Users\Asus\AppData\Roaming\Typora\typora-user-images\image-20220121210542842.png)

#### 开关机

```xml
sync #数据由内存同步到硬盘

shutdown #关机

shutdown -h (+)10(分钟后关机)

shutdown -h now #立刻关机

shutdown -h 20:25

-h +10


-r now#立刻关机

-r +10

reboot #shutdown -r -now

halt # shutdown -h now / poweroff


```

不论重启还是关机都要先sync同步

> 系统目录结构

登录系统后，在当前命令窗口下输入命令：

```
ls /
```

你会看到如下图所示：

![图片](https://mmbiz.qpic.cn/mmbiz_png/uJDAUKrGC7LDkhrDl4H9TqZhwyeNSeaNSvqpApZkrQNCQFhVhyPoPdtFTibRBEssIj6EmiapgETvK2brVhfliaRRg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

树状目录结构：（Linux的一切资源都挂载在这个 / 根节点下）

![图片](https://mmbiz.qpic.cn/mmbiz_png/uJDAUKrGC7LDkhrDl4H9TqZhwyeNSeaNibQYW2xbQIL38lrCCSPEzFKJhCiau0FvQMFSa37NQxTTbbo3PrpjJic5g/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

**以下是对这些目录的解释：**

- **/bin**：bin是Binary的缩写, 这个目录存放着最经常使用的命令。
- **/boot：** 这里存放的是启动Linux时使用的一些核心文件，包括一些连接文件以及镜像文件。
- **/dev ：** dev是Device(设备)的缩写, 存放的是Linux的外部设备，在Linux中访问设备的方式和访问文件的方式是相同的。
- **/etc：** 这个目录用来存放所有的系统管理所需要的配置文件和子目录。
- **/home**：用户的主目录，在Linux中，每个用户都有一个自己的目录，一般该目录名是以用户的账号命名的。
- **/lib**：这个目录里存放着系统最基本的动态连接共享库，其作用类似于Windows里的DLL文件。
- **/lost+found**：这个目录一般情况下是空的，当系统非法关机后，这里就存放了一些文件。
- **/media**：linux系统会自动识别一些设备，例如U盘、光驱等等，当识别后，linux会把识别的设备挂载到这个目录下。
- **/mnt**：系统提供该目录是为了让用户临时挂载别的文件系统的，我们可以将光驱挂载在/mnt/上，然后进入该目录就可以查看光驱里的内容了。
- **/opt**：这是给主机额外安装软件所摆放的目录。比如你安装一个ORACLE数据库则就可以放到这个目录下。默认是空的。
- **/proc**：这个目录是一个虚拟的目录，它是系统内存的映射，我们可以通过直接访问这个目录来获取系统信息。
- **/root**：该目录为系统管理员，也称作超级权限者的用户主目录。
- **/sbin**：s就是Super User的意思，这里存放的是系统管理员使用的系统管理程序。
- **/srv**：该目录存放一些服务启动之后需要提取的数据。
- **/sys**：这是linux2.6内核的一个很大的变化。该目录下安装了2.6内核中新出现的一个文件系统 sysfs 。
- **/tmp**：这个目录是用来存放一些临时文件的。
- **/usr**：这是一个非常重要的目录，用户的很多应用程序和文件都放在这个目录下，类似于windows下的program files目录。
- **/usr/bin：** 系统用户使用的应用程序。
- **/usr/sbin：** 超级用户使用的比较高级的管理程序和系统守护程序。
- **/usr/src：** 内核源代码默认的放置目录。
- **/var**：这个目录中存放着在不断扩充着的东西，我们习惯将那些经常被修改的目录放在这个目录下。包括各种日志文件。
- **/run**：是一个临时文件系统，存储系统启动以来的信息。当系统重启时，这个目录下的文件应该被删掉或清除。

# 目录管理

> 绝对路径和相对路径

我们知道Linux的目录结构为树状结构，最顶级的目录为根目录 `/`。

其他目录通过挂载可以将它们添加到树中，通过解除挂载可以移除它们。

在开始本教程前我们需要先知道什么是绝对路径与相对路径。

> 绝对路径：

路径的写法，由根目录 `/` 写起，例如：`/usr/share/doc` 这个目录。

> 相对路径：

路径的写法，不是由 `/` 写起，例如由 `/usr/share/doc` 要到 `/usr/share/man` 底下时，可以写成：`cd ../man` 这就是相对路径的写法啦！

> 处理目录的常用命令

- ls: 列出目录
- cd：切换目录
- pwd：显示目前的目录
- mkdir：创建一个新的目录
- rmdir：删除一个空的目录
- cp: 复制文件或目录
- rm: 移除文件或目录
- mv: 移动文件与目录，或修改文件与目录的名称

你可以使用 man [命令] 来查看各个命令的使用文档，如 ：man cp。

## ls （列出目录）

在Linux系统当中， ls 命令可能是最常被运行的。

语法：



```
[root@www ~]# ls [-aAdfFhilnrRSt] 目录名称
```

选项与参数：

-a ：全部的文件，连同隐藏文件( 开头为 . 的文件) 一起列出来(常用)

-l ：长数据串列出，包含文件的属性与权限等等数据；(常用)

将目录下的所有文件列出来(含属性与隐藏档)



```
[root@www ~]# ls -al ~
```

补充：



```
ls -A 列出除.及..的其它文件
ls -r 反序排列
ls -t 以文件修改时间排序
ls -S 以文件大小排序
ls -h 以易读大小显示
```

## cd （切换目录）

cd是Change Directory的缩写，这是用来变换工作目录的命令。

语法：



```
cd [相对路径或绝对路径]
```

测试：



```
# 切换到用户目录下
[root@kuangshen /]# cd home  

# 使用 mkdir 命令创建 kuangstudy 目录
[root@kuangshen home]# mkdir kuangstudy

# 进入 kuangstudy 目录
[root@kuangshen home]# cd kuangstudy

# 回到上一级
[root@kuangshen kuangstudy]# cd ..

# 回到根目录
[root@kuangshen kuangstudy]# cd /

# 表示回到自己的家目录，亦即是 /root 这个目录
[root@kuangshen kuangstudy]# cd ~
```

## pwd ( 显示目前所在的目录 )

pwd 是 Print Working Directory 的缩写，也就是显示目前所在目录的命令。



```
[root@kuangshen kuangstudy]#pwd [-P]
```

选项与参数：-P ：显示出确实的路径，而非使用连接(link) 路径。

测试：



```
# 单纯显示出目前的工作目录
[root@kuangshen ~]# pwd
/root

# 如果是链接，要显示真实地址，可以使用 -P参数
[root@kuangshen /]# cd bin
[root@kuangshen bin]# pwd -P
/usr/bin
```

## mkdir （创建新目录）

如果想要创建新的目录的话，那么就使用mkdir (make directory)吧。



```
mkdir [-mp] 目录名称
```

选项与参数：

-m ：配置文件的权限喔！直接配置，不需要看默认权限 (umask) 的脸色～

-p ：帮助你直接将所需要的目录(包含上一级目录)递归创建起来！

测试：



```
# 进入我们用户目录下
[root@kuangshen /]# cd /home

# 创建一个 test 文件夹
[root@kuangshen home]# mkdir test

# 创建多层级目录
[root@kuangshen home]# mkdir test1/test2/test3/test4
mkdir: cannot create directory ‘test1/test2/test3/test4’:
No such file or directory  # <== 没办法直接创建此目录啊！

# 加了这个 -p 的选项，可以自行帮你创建多层目录！
[root@kuangshen home]# mkdir -p test1/test2/test3/test4

# 创建权限为 rwx--x--x 的目录。
[root@kuangshen home]# mkdir -m 711 test2
[root@kuangshen home]# ls -l
drwxr-xr-x 2 root root  4096 Mar 12 21:55 test
drwxr-xr-x 3 root root  4096 Mar 12 21:56 test1
drwx--x--x 2 root root  4096 Mar 12 21:58 test2
```

## rmdir ( 删除空的目录 )

语法：



```
rmdir [-p] 目录名称
```

选项与参数：-p ：连同上一级『空的』目录也一起删除

测试：



```
# 看看有多少目录存在？
[root@kuangshen home]# ls -l
drwxr-xr-x 2 root root  4096 Mar 12 21:55 test
drwxr-xr-x 3 root root  4096 Mar 12 21:56 test1
drwx--x--x 2 root root  4096 Mar 12 21:58 test2

# 可直接删除掉，没问题
[root@kuangshen home]# rmdir test

# 因为尚有内容，所以无法删除！
[root@kuangshen home]# rmdir test1
rmdir: failed to remove ‘test1’: Directory not empty

# 利用 -p 这个选项，立刻就可以将 test1/test2/test3/test4 依次删除。
[root@kuangshen home]# rmdir -p test1/test2/test3/test4
```

注意：这个 rmdir 仅能删除空的目录，你可以使用 rm 命令来删除非空目录，后面我们会将学习！

## cp ( 复制文件或目录 )

语法：



```
[root@www ~]# cp [-adfilprsu] 来源档(source) 目标档(destination)
[root@www ~]# cp [options] source1 source2 source3 .... directory
```

选项与参数：

-a：相当於 -pdr 的意思，至於 pdr 请参考下列说明；(常用)

-p：连同文件的属性一起复制过去，而非使用默认属性(备份常用)；

-d：若来源档为连结档的属性(link file)，则复制连结档属性而非文件本身；

-r：递归持续复制，用於目录的复制行为；(常用)

-f：为强制(force)的意思，若目标文件已经存在且无法开启，则移除后再尝试一次；

-i：若目标档(destination)已经存在时，在覆盖时会先询问动作的进行(常用)

-l：进行硬式连结(hard link)的连结档创建，而非复制文件本身。

-s：复制成为符号连结档 (symbolic link)，亦即『捷径』文件；

-u：若 destination 比 source 旧才升级 destination ！

测试：



```
# 找一个有文件的目录，我这里找到 root目录
[root@kuangshen home]# cd /root
[root@kuangshen ~]# ls
install.sh
[root@kuangshen ~]# cd /home

# 复制 root目录下的install.sh 到 home目录下
[root@kuangshen home]# cp /root/install.sh /home
[root@kuangshen home]# ls
install.sh

# 再次复制，加上-i参数，增加覆盖询问？
[root@kuangshen home]# cp -i /root/install.sh /home
cp: overwrite ‘/home/install.sh’? y # n不覆盖，y为覆盖
```

## rm ( 移除文件或目录 )

语法：



```
rm [-fir] 文件或目录
```

选项与参数：

-f ：就是 force 的意思，忽略不存在的文件，不会出现警告信息；

-i ：互动模式，在删除前会询问使用者是否动作

-r ：递归删除啊！最常用在目录的删除了！这是非常危险的选项！！！

测试：



```
# 将刚刚在 cp 的实例中创建的 install.sh删除掉！
[root@kuangshen home]# rm -i install.sh
rm: remove regular file ‘install.sh’? y
# 如果加上 -i 的选项就会主动询问喔，避免你删除到错误的档名！

# 尽量不要在服务器上使用 rm -rf /
mv  ( 移动文件与目录，或修改名称 )
```

语法：



```
[root@www ~]# mv [-fiu] source destination
[root@www ~]# mv [options] source1 source2 source3 .... directory
```

选项与参数：

-f ：force 强制的意思，如果目标文件已经存在，不会询问而直接覆盖；

-i ：若目标文件 (destination) 已经存在时，就会询问是否覆盖！

-u ：若目标文件已经存在，且 source 比较新，才会升级 (update)

测试：



```
# 复制一个文件到当前目录
[root@kuangshen home]# cp /root/install.sh /home

# 创建一个文件夹 test
[root@kuangshen home]# mkdir test

# 将复制过来的文件移动到我们创建的目录，并查看
[root@kuangshen home]# mv install.sh test
[root@kuangshen home]# ls
test
[root@kuangshen home]# cd test
[root@kuangshen test]# ls
install.sh

# 将文件夹重命名，然后再次查看！
[root@kuangshen test]# cd ..
[root@kuangshen home]# mv test mvtest
[root@kuangshen home]# ls
mvtest
```

# 基本属性

## 看懂文件属性

Linux系统是一种典型的多用户系统，不同的用户处于不同的地位，拥有不同的权限。为了保护系统的安全性，Linux系统对不同的用户访问同一文件（包括目录文件）的权限做了不同的规定。

在Linux中我们可以使用ll或者ls –l命令来显示一个文件的属性以及文件所属的用户和组，如：

[![img](https://images.cnblogs.com/cnblogs_com/kylinxxx/1675669/o_2012180806521608278761(1).png)](https://images.cnblogs.com/cnblogs_com/kylinxxx/1675669/o_2012180806521608278761(1).png)

实例中，boot文件的第一个属性用"d"表示。"d"在Linux中代表该文件是一个目录文件。

在Linux中第一个字符代表这个文件是目录、文件或链接文件等等：

当为[ d ]则是目录

当为[ - ]则是文件；

若是[ l ]则表示为链接文档 ( link file )；

若是[ b ]则表示为装置文件里面的可供储存的接口设备 ( 可随机存取装置 )；

若是[ c ]则表示为装置文件里面的串行端口设备，例如键盘、鼠标 ( 一次性读取装置 )。

接下来的字符中，以三个为一组，且均为『rwx』 的三个参数的组合。

其中，[ r ]代表可读(read)、[ w ]代表可写(write)、[ x ]代表可执行(execute)。

要注意的是，这三个权限的位置不会改变，如果没有权限，就会出现减号[ - ]而已。

每个文件的属性由左边第一部分的10个字符来确定（如下图）：

[![img](https://www.runoob.com/wp-content/uploads/2014/06/363003_1227493859FdXT.png)](https://www.runoob.com/wp-content/uploads/2014/06/363003_1227493859FdXT.png)

从左至右用0-9这些数字来表示。

第0位确定文件类型，第1-3位确定属主（该文件的所有者）拥有该文件的权限。第4-6位确定属组（所有者的同组用户）拥有该文件的权限，第7-9位确定其他用户拥有该文件的权限。

其中：

第1、4、7位表示**读权限**，如果用"r"字符表示，则有读权限，如果用"-"字符表示，则没有读权限；

第2、5、8位表示**写权限**，如果用"w"字符表示，则有写权限，如果用"-"字符表示没有写权限；

第3、6、9位表示**可执行权限**，如果用"x"字符表示，则有执行权限，如果用"-"字符表示，则没有执行权限。

对于文件来说，它都有一个特定的所有者，也就是对该文件具有所有权的用户。

同时，在Linux系统中，用户是按组分类的，一个用户属于一个或多个组。

文件所有者以外的用户又可以分为文件所有者的同组用户和其他用户。

因此，Linux系统按文件所有者、文件所有者同组用户和其他用户来规定了不同的文件访问权限。

在以上实例中，boot 文件是一个目录文件，属主和属组都为 root。

# 修改文件属性

### 文件修改相关

#### 创建一个文件：

cd ~
vim word.txt
通过上面的命令就打开了vim编辑器。（需要注意的是，使用vim编辑器创建一个文件时，不管是否有扩展名.txt，都是生成文本文件。）

#### 进入编辑模式：

在键盘上按下字母i或者a即可开始编辑啦！

#### 退出编辑模式：

方式一：按下esc键，输入“:wq”3个英文字符，然后按下Enter键，表示保存文件并退出。
方式二：按下esc键，输入“:q”两个英文字符，然后按下Enter键，表示不保存并退出。（如果本次编辑没有修改内容，则可以顺利退出；否则，vim编辑器是不会允许你退出的，这时如果想要不保存退出，就要使用下面的方法三）
方式三：按下esc键，输入“:q!”3个英文字符，然后按Enter键，表示不保存并强制退出。

## chgrp：更改文件属组



```
chgrp [-R] 属组名 文件名
```

-R：递归更改文件属组，就是在更改某个目录文件的属组时，如果加上-R的参数，那么该目录下的所有文件的属组都会更改。

## chown：更改文件属主，也可以同时更改文件属组



```
chown [–R] 属主名 文件名
chown [-R] 属主名：属组名 文件名
```

## **chmod：更改文件9个属性**



```
chmod [-R] xyz 文件或目录
```

Linux文件属性有两种设置方法，一种是数字，一种是符号。

Linux文件的基本权限就有九个，分别是owner/group/others三种身份各有自己的read/write/execute权限。

先复习一下刚刚上面提到的数据：文件的权限字符为：『-rwxrwxrwx』， 这九个权限是三个三个一组的！其中，我们可以使用数字来代表各个权限，各权限的分数对照表如下：

r:4 w:2 x:1

100 10 1

每种身份(owner/group/others)各自的三个权限(r/w/x)分数是需要累加的，例如当权限为：[-rwxrwx---] 分数则是：

owner = rwx = 4+2+1 = 7

group = rwx = 4+2+1 = 7

others= --- = 0+0+0 = 0



```
chmod 770 filename
```

# 文件内容查看

## 概述

Linux系统中使用以下命令来查看文件的内容：

- cat 由第一行开始显示文件内容
- tac 从最后一行开始显示，可以看出 tac 是 cat 的倒着写！
- nl 显示的时候，顺道输出行号！
- more 一页一页的显示文件内容
- less 与 more 类似，但是比 more 更好的是，他可以往前翻页！
- head 只看头几行
- tail 只看尾巴几行

你可以使用 man [命令]来查看各个命令的使用文档，如 ：man cp。

## cat 由第一行开始显示文件内容

语法：



```
cat [-AbEnTv]
```

选项与参数：

-A ：相当於 -vET 的整合选项，可列出一些特殊字符而不是空白而已；

-b ：列出行号，仅针对非空白行做行号显示，空白行不标行号！

-E ：将结尾的断行字节 $ 显示出来；

-n ：列印出行号，连同空白行也会有行号，与 -b 的选项不同；

-T ：将 [tab] 按键以 ^I 显示出来；

-v ：列出一些看不出来的特殊字符

测试：



```
# 查看网络配置: 文件地址 /etc/sysconfig/network-scripts/
[root@kuangshen ~]# cat /etc/sysconfig/network-scripts/ifcfg-eth0
DEVICE=eth0
BOOTPROTO=dhcp
ONBOOT=yes
```

## tac

tac与cat命令刚好相反，文件内容从最后一行开始显示，可以看出 tac 是 cat 的倒着写！如：



```
[root@kuangshen ~]# tac /etc/sysconfig/network-scripts/ifcfg-eth0
ONBOOT=yes
BOOTPROTO=dhcp
DEVICE=eth0
```

## nl 显示行号

语法：



```
nl [-bnw] 文件
```

选项与参数：

-b ：指定行号指定的方式，主要有两种：-b a ：表示不论是否为空行，也同样列出行号(类似 cat -n)；-b t ：如果有空行，空的那一行不要列出行号(默认值)；

-n ：列出行号表示的方法，主要有三种：-n ln ：行号在荧幕的最左方显示；-n rn ：行号在自己栏位的最右方显示，且不加 0 ；-n rz ：行号在自己栏位的最右方显示，且加 0 ；

-w ：行号栏位的占用的位数。

测试：



```
[root@kuangshen ~]# nl /etc/sysconfig/network-scripts/ifcfg-eth0
1DEVICE=eth0
2BOOTPROTO=dhcp
3ONBOOT=yes
```

## more 一页一页翻动

在 more 这个程序的运行过程中，你有几个按键可以按的：

空白键 (space)：代表向下翻一页；

Enter ：代表向下翻『一行』；

/字串 ：代表在这个显示的内容当中，向下搜寻『字串』这个关键字；

:f ：立刻显示出档名以及目前显示的行数；

q ：代表立刻离开 more ，不再显示该文件内容。

b 或 [ctrl]-b ：代表往回翻页，不过这动作只对文件有用，对管线无用。



```
[root@kuangshen etc]# more /etc/csh.login
....(中间省略)....
--More--(28%) # 重点在这一行喔！你的光标也会在这里等待你的命令
```

## less 一页一页翻动，以下实例输出/etc/man.config文件的内容：

less运行时可以输入的命令有：

空白键 ：向下翻动一页；

[pagedown]：向下翻动一页；

[pageup] ：向上翻动一页；

/字串 ：向下搜寻『字串』的功能；

?字串 ：向上搜寻『字串』的功能；

n ：重复前一个搜寻 (与 / 或 ? 有关！)

N ：反向的重复前一个搜寻 (与 / 或 ? 有关！)

q ：离开 less 这个程序；



```
[root@kuangshen etc]# more /etc/csh.login
....(中间省略)....
:   # 这里可以等待你输入命令！
```

## head 取出文件前面几行

语法：



```
head [-n number] 文件
```

选项与参数：-n 后面接数字，代表显示几行的意思！

默认的情况中，显示前面 10 行！若要显示前 20 行，就得要这样：



```
[root@kuangshen etc]# head -n 20 /etc/csh.login
```

## tail 取出文件后面几行

语法：



```
tail [-n number] 文件
```

选项与参数：

-n ：后面接数字，代表显示几行的意思

默认的情况中，显示最后 10 行！若要显示最后 20 行，就得要这样：



```
[root@kuangshen etc]# tail -n 20 /etc/csh.login
```

# 拓展：Linux 链接概念

Linux 链接分两种，一种被称为硬链接（Hard Link），另一种被称为符号链接（Symbolic Link）。

情况下，ln 命令产生硬链接。

> 硬连接

硬连接指通过索引节点来进行连接。在 Linux 的文件系统中，保存在磁盘分区中的文件不管是什么类型都给它分配一个编号，称为索引节点号(Inode Index)。在 Linux 中，多个文件名指向同一索引节点是存在的。比如：A 是 B 的硬链接（A 和 B 都是文件名），则 A 的目录项中的 inode 节点号与 B 的目录项中的 inode 节点号相同，即一个 inode 节点对应两个不同的文件名，两个文件名指向同一个文件，A 和 B 对文件系统来说是完全平等的。删除其中任何一个都不会影响另外一个的访问。

硬连接的作用是允许一个文件拥有多个有效路径名，这样用户就可以建立硬连接到重要文件，以防止“误删”的功能。其原因如上所述，因为对应该目录的索引节点有一个以上的连接。只删除一个连接并不影响索引节点本身和其它的连接，只有当最后一个连接被删除后，文件的数据块及目录的连接才会被释放。也就是说，文件真正删除的条件是与之相关的所有硬连接文件均被删除。

> 软连接

另外一种连接称之为符号连接（Symbolic Link），也叫软连接。软链接文件有类似于 Windows 的快捷方式。它实际上是一个特殊的文件。在符号连接中，文件实际上是一个文本文件，其中包含的有另一文件的位置信息。比如：A 是 B 的软链接（A 和 B 都是文件名），A 的目录项中的 inode 节点号与 B 的目录项中的 inode 节点号不相同，A 和 B 指向的是两个不同的 inode，继而指向两块不同的数据块。但是 A 的数据块中存放的只是 B 的路径名（可以根据这个找到 B 的目录项）。A 和 B 之间是“主从”关系，如果 B 被删除了，A 仍然存在（因为两个是不同的文件），但指向的是一个无效的链接。

测试：



```
[root@kuangshen /]# cd /home
[root@kuangshen home]# touch f1 # 创建一个测试文件f1
[root@kuangshen home]# ls
f1
[root@kuangshen home]# ln f1 f2     # 创建f1的一个硬连接文件f2
[root@kuangshen home]# ln -s f1 f3   # 创建f1的一个符号连接文件f3
[root@kuangshen home]# ls -li       # -i参数显示文件的inode节点信息
397247 -rw-r--r-- 2 root root     0 Mar 13 00:50 f1
397247 -rw-r--r-- 2 root root     0 Mar 13 00:50 f2
397248 lrwxrwxrwx 1 root root     2 Mar 13 00:50 f3 -> f1
```

从上面的结果中可以看出，硬连接文件 f2 与原文件 f1 的 inode 节点相同，均为 397247，然而符号连接文件的 inode 节点不同。



```
# echo 字符串输出 >> f1 输出到 f1文件
[root@kuangshen home]# echo "I am f1 file" >>f1
[root@kuangshen home]# cat f1
I am f1 file
[root@kuangshen home]# cat f2
I am f1 file
[root@kuangshen home]# cat f3
I am f1 file
[root@kuangshen home]# rm -f f1
[root@kuangshen home]# cat f2
I am f1 file
[root@kuangshen home]# cat f3
cat: f3: No such file or directory
```

通过上面的测试可以看出：当删除原始文件 f1 后，硬连接 f2 不受影响，但是符号连接 f1 文件无效；

依此您可以做一些相关的测试，可以得到以下全部结论：

删除符号连接f3,对f1,f2无影响；

删除硬连接f2，对f1,f3也无影响；

删除原文件f1，对硬连接f2没有影响，导致符号连接f3失效；

同时删除原文件f1,硬连接f2，整个文件会真正的被删除。



# Shell 脚本编程

2022/8/25

服务器集群管理

J2EE,写一些SHELL脚本或程序对服务器进行维护(比如定时备份数据库脚本)

大数据必备

Shell:命令行解释器，提供一个向Linux内核发送请求，以便运行程序的界面系统及程序

![image-20220825114131275](.\photo\image-20220825114131275.png)

mk dir/opt/t(xxx.sh) => Shell(命令行解释) - >Linux内核

常用Bash (Basic Shell)

##### 1、shell脚本优势

```
批量处理，自动化
减少人为失误
```

##### 2、shell底层的实现原理

```
shell底层也是用c语言实现的，--可以实现操控操作系统
```

#### 入门

开头 #!/bin/bash

脚本需要可执行权限

1.通过输入脚本相对路径或绝对路径运行(需要可执行权限)

通过 chmod u+x hello.sh 可以添加

```bash
#!/bin/bash
echo "hello,world~"
```

```bash
[root@lgdlgd123 shcode]# vim hello.sh
[root@lgdlgd123 shcode]# ls
hello.sh
[root@lgdlgd123 shcode]# ll
total 4
-rw-r--r-- 1 root root 32 Aug 25 14:38 hello.sh
# 此时还没有执行权限
[root@lgdlgd123 shcode]# ./hello.sh
-bash: ./hello.sh: Permission denied
#增加权限
[root@lgdlgd123 shcode]# chmod u+x hello.sh
[root@lgdlgd123 shcode]# ll
total 4
-rwxr--r-- 1 root root 32 Aug 25 14:38 hello.sh
[root@lgdlgd123 shcode]# ./hello.sh
hello,world~
```

2.直接通过 sh xxx.sh运行(不需要可执行权限)



#### 变量介绍

系统变量：$HOME , $PWD, $SHELL, $USER ，可以通过echo $HOME 

显示当前shell所有变量: set

用户自定义变量：

- 定义变量: xx=x
- 撤销: unset变量
- 静态变量声明: readonly变量 (不能unset)

快速入门

1. 定义变量A
2. 撤销变量
3. 声明静态变量B=2，不可unset
4. 把变量提升为全局变量，供其他shell程序使用

```bash
[root@lgdlgd123 shcode]# cat var.sh
#!/bin/bash
A=100
#定义不用$ 输出需要$
echo A=$A

echo "A=$A"
#撤销变量A
unset A

echo "A=$A"
#静态变量
readonly B=2
echo "B=$B"

unset B
```

变量规则

变量不可以用数字开头，等号两侧不可以有空格

变量名称习惯为大写

利用反引号，把运行里面的命令的返回值传给A

```java
A=`date`
A=$(date)
```

#### 设置环境变量

环境变量 ，即全局变量

- export xx=xx 将变量输出为环境变量

  注意输出环境变量前，需要让其生效

  使用 source /etc/profile

  ```bash
  #直接拿，没生效
  [root@lgdlgd123 opt]# echo $TOMCAT_HONE
  #让修改后的配置信息立即生效
  [root@lgdlgd123 opt]# source /etc/profile
  [root@lgdlgd123 opt]# echo $TOMCAT_HOME
  /www/server/tomcat
  ```

shell脚本多行注释

```bash
 22 :<<!
 23 C=`date`
 24 lalalaa
 25 lalala=a
 26 echo lalala 
 27 !
```

#### 位置参数变量

- $0~$9，${10} 代表第几个参数
- $* 代表命令行中所有的参数，看作一个整体
- $@ 代表命令行中所有的参数，不过$@把每个参数区别对待
- $# 代表命令行中所有参数个数

```bash
[root@lgdlgd123 shcode]# sh position.sh 1 2 3 4 5
position.sh #$0
1 #$1
1 2 3 4 5
1 2 3 4 5
5
```

#### 预定义变量

实现定义好的变量

- $$ 获取当前进程PID
- #! 获取后台运行的最后一个进程PID
- #? 最后一次执行的命令的返回状态。如果为0，说明上一条命令正确执行。否则非0说明执行不正确

```bash
[root@lgdlgd123 shcode]# sh preVar.sh
当前执行的进程id=2654
最后一个后台方式运行的PID id=2655
执行的结果是0
[root@lgdlgd123 shcode]# /root/shcode/position.sh



0
```

#### 运算符

- expr m + n (注意expr运算符间要有空格，\* / % 除乘取余)   或者 "$((运算式))" 或  "$[运算式]"（推荐使用）

  ```bash
  [root@lgdlgd123 shcode]# cat express.sh
  #/bin/bash
  A=$[( 2 + 3 ) * 4]
  echo $A
  
  B=$1
  C=$2
  D=$[ $B + $C]
  #D=`expr 2 + 3`
  echo $D
  
  [root@lgdlgd123 shcode]# sh express.sh 1 2
  20
  3
  ```

#### 条件判断

![image-20220825164200730](.\photo\image-20220825164200730.png)

```bash
[root@lgdlgd123 shcode]# sh TiaoJian.sh
equal
[root@lgdlgd123 shcode]# cat TiaoJian.sh
#/bin/bash
if [ "ok" = "ok" ]
then
        echo "equal"
elif [ "ok" = "ok2" ]
then
        echo "noequal1"
else
        echo "noequal"
fi
```

#### for循环

```bash
[root@lgdlgd123 shcode]# sh forXunHuan.sh
hello 0
hello 1
hello 3
[root@lgdlgd123 shcode]# cat forXunHuan.sh
#/bin/bash
SUM=0
for ((i=0;i<3;i++))
do
        SUM=$[$SUM + $i]
        echo "hello $SUM"
done
```

seq 进制

```bash
[root@lgdlgd123 shcode]# sh seqTest.sh
seqTest 1
seqTest 3
seqTest 5
seqTest 7
seqTest 9
seqTest 11
seqTest 13
seqTest 15
seqTest 17
seqTest 19
[root@lgdlgd123 shcode]# cat seqTest.sh
#/bin/bash
#seq 起始 步长 最大
for i in `seq 1 2 20`
do
        echo "seqTest $i"
done
```

使用{1..10}方式

```bash
for i in {1..10}
do
        echo "{}Test $i"
done
```

#### case语句

```bash
[root@lgdlgd123 shcode]# sh caseTest.sh 2
周二
[root@lgdlgd123 shcode]# cat caseTest.sh
#/bin/bash
case $1 in 
"1")
echo "周一"
;;
"2")
echo "周二"
;;
"3")
echo "周三"
;;
"4")
echo "周四"
;;
"5")
echo "周五"
;;
esac
```

#### while循环

```bash
[root@lgdlgd123 shcode]# cat whileTest.sh
#/bin/bash
SUM=0
i=0
while [ $i -lt $1 ]
do
        i=$[ $i + 1  ]
        SUM=$[ $SUM + $i ]
done
echo "$SUM"
[root@lgdlgd123 shcode]# sh whileTest.sh 4
10
```

#### read语法

```bash
[root@lgdlgd123 shcode]# sh readTest.sh
请输入一个数NUM1=40
您输入的数为40
请在10秒内输入两个数NUM2 NUM33 5
您输入的数为3 5
[root@lgdlgd123 shcode]# sh readTest.sh
请输入一个数NUM1=5
您输入的数为5
请在10秒内输入两个数NUM2 NUM3 您输入的数为 
[root@lgdlgd123 shcode]#  cat readTest.sh
#!/bin/bash
read -p "请输入一个数NUM1=" NUM1
echo "您输入的数为$NUM1"
read -t 10 -p "请在10秒内输入两个数NUM2 NUM3" NUM2 NUM3
echo "您输入的数为$NUM2 $NUM3"
```

函数

basename：返回完整路径/最后部分，用于获取文件名，他会删除前缀

```bash
[root@lgdlgd123 ~]# basename /root/shcode/readTest.sh .sh
readTest
```

dirname用于返回完整路径前面的部分,即文件所在路径(不含文件名)

```bash
[root@lgdlgd123 ~]# dirname /root/shcode/readTest.sh
/root/shcode
```

#### 自定义函数

[function] funname[()]

{

​	Action;

​	[return int;]

}

funname [值]

```bash
[root@lgdlgd123 ~]# sh HanShuTest.sh
请输入n1 与 n2 的值50 40
和为90
[root@lgdlgd123 ~]# cat HanShuTest.sh
#!/bin/bash
function getSum(){

        SUM=$[ $n1 + $n2 ]
        echo "和为$SUM"
}

read -p "请输入n1 与 n2 的值" n1 n2

getSum $n1 $n2 
```

### 示例——备份数据库

简单备份一下javaweb-test里的user表

由crond 来定时调用 写好的shell脚本即可

```bash
[root@lgdlgd123 sbin]# sh mysql_db_backup.sh 
2022-08-26_101112
mysqldump: [Warning] Using a password on the command line interface can be insecure.
[root@lgdlgd123 sbin]# cd /db
-bash: cd: /db: No such file or directory
[root@lgdlgd123 sbin]# ls
db  mysql_db_backup.sh
[root@lgdlgd123 sbin]# cd db
[root@lgdlgd123 db]# ls
2022-08-26_101112
[root@lgdlgd123 db]# cd 2022-08-26_101112/
[root@lgdlgd123 2022-08-26_101112]# gunzip
gzip: compressed data not read from a terminal. Use -f to force decompression.
For help, type: gzip -h
[root@lgdlgd123 2022-08-26_101112]# gunzip 2022-08-26_101112.sql.gz 
[root@lgdlgd123 2022-08-26_101112]# cat 2022-08-26_101112.sql
```

脚本

```bash
[root@lgdlgd123 sbin]# cat mysql_db_backup.sh 
#!/bin/bash
BACKUP=/user/sbin/db
DATETIME=$(date +%Y-%m-%d_%H%M%S)
echo $DATETIME
HOST=localhost
DB_USER=root
DB_PWD=1328910
DATABASE=javaweb-test

#如果不存在目录，创建目录
[ ! -d "${BACKUP}/${DATETIME}" ] && mkdir -p "${BACKUP}/${DATETIME}"

#备份数据库
mysqldump -u${DB_USER} -p${DB_PWD} --host=${HOST} -q -R --databases ${DATABASE} | gzip > ${BACKUP}/${DATETIME}/$DATETIME.sql.gz
#将文件处理为 tar.gz
cd ${BACKUP} 
tar -zcvf $DATETIME.tar.gz ${DATETIME}
#删除 备份的文件夹
rm -rf ${BACKUP}/${DATETIME}
#删除10天前的备份文件
find ${BACKUP} -atime +10 -name "*.tar.gz" -exec rm {} \;
echo "备份成功"
```

最后通过crontab进行定时执行sh

```bash
[root@lgdlgd123 sbin]# crontab --help
crontab: invalid option -- '-'
crontab: usage error: unrecognized option
Usage:
 crontab [options] file
 crontab [options]
 crontab -n [hostname]

Options:
 -u <user>  define user
 -e         edit user's crontab
 -l         list user's crontab
 -r         delete user's crontab
 -i         prompt before deleting
 -n <host>  set host in cluster to run users' crontabs
 -c         get host in cluster to run users' crontabs
 -s         selinux context
 -x <mask>  enable debugging

Default operation is replace, per 1003.2
[root@lgdlgd123 sbin]# crontab -l
30 10 * * * /user/sbin/mysql_db_backup.sh
```

# VIM 加快开发

Linux内置vi文本编辑器

常用三种模式

- 正常模式

  可以上下左右移动光标，删除字符，复制黏贴

- 插入模式

  i,I,oO,aA,r,R进入编辑模式

- 命令行模式

  相关指令，读取，存盘，替换，显示行号

![image-20220826111708459](.\photo\image-20220826111708459.png)

- 输入vim xxx.xxx进入编辑的一般模式

- 按下i,I,oO,aA,r,R进入编辑模式

- 编辑完按下Esc，进入命令模式

  输入:wq(保存退出)

  ​		:q(退出)

  ​		:q!（强制退出保存）

![image-20220826111323992](.\photo\image-20220826111323992.png)

| 快捷键    | 功能                                                         |
| --------- | ------------------------------------------------------------ |
| u（小写） | undo 的第 1 个字母，功能是撤销最近一次对文本做的修改操作。   |
| Ctrl+R    | Redo 的第 1 个字母，功能是恢复最近一次所做的撤销操作。       |
| U（大写） | 第一次会撤销对一行文本（光标所在行）做过的全部操作，第二次使用该命令会恢复对该行文本做过的所有操作。 |

数字 +shift + g快速定位到某行

![image-20220826144920078](.\photo\image-20220826144920078.png)
