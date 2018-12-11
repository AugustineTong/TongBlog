---
layout:     post

title:      Docker--Linux--基础

date:       2018-12-13

author:     Augustine Tong

header-img: img/steve.jpg

catalog: true

tags:
    - Docker
    - Linux
---

# Docker--Linux--基础
**代码均为自己手打过一次, 输出结果均为自己的输出结果**.  

## 获取镜像
<pre>
docker pull [选项] [Docker Registry 地址[:端口号]/]仓库名[:标签]
</pre>
- Docker 镜像仓库地址：地址的格式一般是 <域名/IP>[:端口号]。默认地址是 Docker Hub。  
- 仓库名：如之前所说，这里的仓库名是两段式名称，即 <用户名>/<软件名>。对于 Docker Hub，如果不给出用户名，则默认为 library，也就是官方镜像。

<pre>
docker pull ubuntu:18.04

docker run -it --rm ubuntu:18.04 bash
</pre>
-it：这是两个参数，一个是 -i：交互式操作，一个是 -t 终端。我们这里打算进入 bash 执行一些命令并查看返回结果，因此我们需要交互式终端。  
--rm：这个参数是说容器退出后随之将其删除。默认情况下，为了排障需求，退出的容器并不会立即删除，除非手动 docker rm。我们这里只是随便执行个命令，看看结果，不需要排障和保留结果，因此使用 --rm 可以避免浪费空间。  
ubuntu:18.04：这是指用 ubuntu:18.04 镜像为基础来启动容器。  
bash：放在镜像名后的是命令，这里我们希望有个交互式 Shell，因此用的是 bash。  

<pre>
docker image ls
docker image rm [选项] <镜像1> [<镜像2> ...]
</pre>

## 操作容器
<pre>
docker run ubuntu:14.04 /bin/echo 'Hello world'
</pre>
这跟在本地直接执行 /bin/echo 'hello world' 几乎感觉不出任何区别。  

下面的命令则启动一个 bash 终端，允许用户进行交互。  

<pre>
docker run -t -i ubuntu:14.04 /bin/bash
</pre>
其中，-t 选项让Docker分配一个伪终端（pseudo-tty）并绑定到容器的标准输入上，-i 则让容器的标准输入保持打开。  

## Linux 操作
以ls指令列出"~"目录下的所有隐藏档与相关的文件属性，要达到这一要求需要加入 -al 这样的选项
<pre>

ls -al ~

ls       -al    ~

ls -a -l ~

// 上面这三个指令的下达方式是一模一样的执行结果  

date // 显示日期和时间
Date // 找不到命令
DATE // 找不到命令 

date
Tue Dec 11 11:46:20 UTC 2018

date +%Y/%m/%d
2018/12/11

date +%H:%M
11:46

注意，仅CentOS中
显示日历

cal


    December 2018   
Su Mo Tu We Th Fr Sa
                   1
 2  3  4  5  6  7  8
 9 10 11 12 13 14 15
16 17 18 19 20 21 22
23 24 25 26 27 28 29
30 31


cal 2018

       October               November               December      
Su Mo Tu We Th Fr Sa   Su Mo Tu We Th Fr Sa   Su Mo Tu We Th Fr Sa
    1  2  3  4  5  6                1  2  3                      1
 7  8  9 10 11 12 13    4  5  6  7  8  9 10    2  3  4  5  6  7  8
14 15 16 17 18 19 20   11 12 13 14 15 16 17    9 10 11 12 13 14 15
21 22 23 24 25 26 27   18 19 20 21 22 23 24   16 17 18 19 20 21 22
28 29 30 31            25 26 27 28 29 30      23 24 25 26 27 28 29
                                              30 31



cal [month] [year]

cal 2 2019

    February 2019   
Su Mo Tu We Th Fr Sa
                1  2
 3  4  5  6  7  8  9
10 11 12 13 14 15 16
17 18 19 20 21 22 23
24 25 26 27 28

简单好用的计算器bc

bc

坑：Docker中的Centos镜像最开始并没有bc，需要自己安装

yum install man
yum install bc

bc计算器里面，运算符

+
-
*
/
^ 指数
% 余数

预设10 / 100 = 0
可以先执行
scale = 3
这样结果均为三位小数

</pre>

### Linux 重要热键

<pre>
    
ca [tab][tab]

发现会显示所有以ca开头的指令
两次tab如果在command后面，那就代表着【命令补齐】

ls -al ~/.bash  [tab][tab]
在该目录下面所有以 .bash 为开头的文件名都会被显示出来
两次tab如果在文件名后面，那就是【文件补齐】

坑：Docker中的Centos镜像最开始并没有bash-completion ，需要自己安装

yum install bash-completion 

两次tab也可能是【参数补齐】


[Ctrl]-c
中断目前程序

[Ctrl]-d
键盘输入结束EOF
可取代exit


</pre>


## Docker 容器操作
例如刚才已经yum install 了，如果不保存结果，那么下次会要同样的流程再来一遍 install  

最好的解决方法是保存  
<pre>
docker ps -l
获取最后一次更改的容器id

docker ps -a
获取所有的容器id

无需拷贝完整的id，通常来讲最开始的三至四个字母即可区分
但是全拷贝更稳一点
docker commit 3625ff5e4825 augu/centos

但我之前已有过一个augu/centos
为了更好地区分，最好还是去掉原来的那一个
怎么删除容器呢
docker container ls -a
查看所有已创建的容器

清理所有处于终止状态的容器
docker container prune

删除单独一个容器
docker container rm 容器名/id

列出本地image镜像
docker image ls

删除镜像
docker image rm 容器名/id

</pre>

## Linux

<pre>

date --help
可以把使用的指令的用法做一个大致的了解

回到Mac的$界面后
可以输入
man date

进入manual后
输入/date
即可搜索所有的date关键字

cd [相对路径/绝对路径]
变换目录

cd ~augu
去到augu这个用户的家目录，即/home/augu

cd
cd ~
回到自己的家目录，即/root 这个目录

cd ..
回到上层的目录

cd /var/spool/mail

cd ../postfix

pwd
显示当前目录

cd /var/mail

pwd
/var/mail

pwd -P
/var/spool/mail

加上 pwd -P 的选项后，会不以连结文件的数据显示，而是显示正确的完整路径

mkdir 
mkdir [-mp] 目录名称
-m :配置文件案的权限，直接设定，不需要看预设权限
-p :帮助你直接将所需要的目录(包含上层目录)递归建立起来

cd /tmp
mkdir test
mkdir test1/test2/test3/test4
mkdir: cannot create directory ‘test1/test2/test3/test4’: No such file or directory

mkdir -p test1/test2/test3/test4
加了这个 -p 的选项，可以自行帮你建立多层目录


mkdir -m 711 test2
如果没有加上 -m 来强制设定属性，系统会使用默认属性
我们给予 -m 711 来给予新的目录 drwx--x--x 的权限
ls -ld test*
来查看mkdir的权限


rmdir [-p] 目录名称
删除一个空目录

ls -ld test*
查看有多少目录存在

rmdir test2
可以，test2为空

rmdir test1
不行，里面非空

rmdir -p test1/test2/test3/test4

ls -ld test*

</pre>

## 执行文件路径的变量

<pre>
su -
转到管理员权限

echo $PATH
输出PATH

PATH="${PATH}:/root"
增加一条PATH

echo $PATH
输出PATH

每个目录中间用冒号(:)来隔开， 每个目 录是有『顺序』之分的


ls -al ~
将家目录下的所有文件列出来(含属性与隐藏文件)

ls -alF --color=never ~
不显示颜色，但在文件名末显示出该文件名代表的类型(type)



</pre>



