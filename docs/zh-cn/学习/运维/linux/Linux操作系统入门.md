



#	1.什么是Linux

Linux 内核最初只是由芬兰人林纳斯·托瓦兹（Linus Torvalds）在赫尔辛基大学上学时出于个人爱好而编写的。

Linux 是一套免费使用和自由传播的类 Unix 操作系统，是一个基于 POSIX 和 UNIX 的多用户、多任务、支持多线程和多 CPU 的操作系统。

Linux 能运行主要的 UNIX 工具软件、应用程序和网络协议。它支持 32 位和 64 位硬件。Linux 继承了 Unix 以网络为核心的设计思想，是一个性能稳定的多用户网络操作系统。目前国内 Linux 更多的是应用于服务器上。



Linux 的发行版说简单点就是将 Linux 内核与应用软件做一个打包。

目前市面上较知名的发行版有：Ubuntu、RedHat、CentOS、Debian、Fedora、SuSE、OpenSUSE、Arch Linux、SolusOS 等。

![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/1578737899.png)



#	2.Linux安装

VMware——虚拟机 

centos7——操作系统 

xmanager——xshell和xftp进⾏远程连接



安装教程：http://c.biancheng.net/view/714.html





#	3.网络配置

##	a.虚拟机网络

​		虚拟机上⽹⼀般情况下使⽤nat虚拟机共享主机的IP地址。

​		打开vmware虚拟机，我们可以在选项栏的“编辑”下的“虚拟⽹络编辑器”中看到 VMnet0(桥接模式)、VMnet1(仅主机模式)、VMnet8(NAT模式)



##	b.Linux网络配置

&emsp;	因为ip地址默认是动态的，有时候重启linux就可能更换ip地址，这样xshell中配置的ip链接就会失效，所以我们可以将ip设置为静态的。

​		查看IP地址： ip add 

​		编辑IP地址：vim  /etc/sysconﬁg/network-scripts/ifcfg-ens33   

​		i进⼊编辑模式  

​		esc进⼊只读模式   

​		hift+：进⼊低⾏的命令⾏模式     

​		wq!：  w写   q退出   ！强制



![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/1578740259.png)





```shell
[root@localhost ~]# vim /etc/sysconfig/network-scripts/ifcfg-ens32 

TYPE=Ethernet
PROXY_METHOD=none
BROWSER_ONLY=no
BOOTPROTO=static
DEFROUTE=yes
NAME=ens32
UUID=7f036108-5202-4fb2-a413-f4adb23710ca
DEVICE=ens32
ONBOOT=yes
IPADDR=192.168.77.130
NETMASK=255.255.255.0
GATEWAY=192.168.77.2
DNS1=8.8.8.8

[root@localhost ~]# systemctl restart network.service
[root@localhost ~]# reboot
```





#	4.XShell的使用

##	a.介绍

​		Xshell 是⼀个强⼤的安全终端模拟软件，它⽀持SSH1, SSH2, 以及Microsoft Windows 平台 的TELNET 协议。Xshell 通过互联⽹到远程主机的安全连接以及它创新性的设计和特⾊帮助 ⽤户在复杂的⽹络环境中享受他们的⼯作。

​		Xshell可以在Windows界⾯下⽤来访问远端不同系统下的服务器，从⽽⽐较好的达到远程控制 终端的⽬的。除此之外，其还有丰富的外观配⾊⽅案以及样式选择。



##	b.作用

​	作⽤： Xshell是Windows下⼀款功能⾮常强⼤的安全终端模拟软件，⽀持Telnet、Rlogin、SSH、 SFTP、Serial 等协议，可以⾮常⽅便的对linux主机进⾏远程管理。



##	c.SSH

​		Secure Shell(SSH) 是由 IETF(The Internet Engineering Task Force) 制定的建⽴在应⽤层基 础上的安全⽹络协议。它是专为远程登录会话(甚⾄可以⽤Windows远程登录Linux服务器进⾏ ⽂件互传)和其他⽹络服务提供安全性的协议，可有效弥补⽹络中的漏洞。通过SSH，可以把 所有传输的数据进⾏加密，也能够防⽌DNS欺骗和IP欺骗。还有⼀个额外的好处就是传输的数 据是经过压缩的，所以可以加快传输的速度。⽬前已经成为Linux系统的标准配置。

​		SSH只是⼀种协议，存在多种实现，既有商业实现，也有开源实现。



##	d.Xshell中乱码问题解决

​		查看在虚拟机下终端中是否会乱码，如果不会乱码就是xshell的编码格式不⽀持， 打开xshell,选择ﬁle-->properties-->terminal，修改编码为utf-8，重启xshell即可

![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/1578741127.png)





#	5.防火墙配置

- 1. 查看防⽕墙服务状态ﬁrewalld  ：`systemctl status ﬁrewalld`

  2. 查看防⽕墙状态 ：`ﬁrewall-cmd --state`

  3. 开启、重启、关闭、ﬁrewalld.service服务

     \# 开启 `service ﬁrewalld start`

     \# 重启 `service ﬁrewalld restart `

     \# 关闭 `service ﬁrewalld stop` 

     \#关闭`systemctl stop firewalld`

  4. 查看防⽕墙规则：`ﬁrewall-cmd --list-all`

  5. 查询、开放、关闭端⼝

     \# 查询端⼝是否开放             `ﬁrewall-cmd --query-port=8080/tcp`         

     \# 开放80端⼝            `ﬁrewall-cmd --permanent --add-port=80/tcp`         

     \# 移除端⼝         `ﬁrewall-cmd --permanent --remove-port=8080/tcp`

     \# 重启防⽕墙( 修改配置后要重启防⽕墙 )             `ﬁrewall-cmd --reload`

     \#参数解释

     * firwall--cmd：是Linux提供的操作frewall的一个工具

     * --permanent：表示设为持久

     * --add-port：标识添加的端口

  6. ip的配置

     * 查看iptables状态 `service iptables status`       

     * 开启/关闭`iptables service iptables start service iptables stop`      

     * 查看iptables是否开机启动 `chkconﬁg iptables --list `      

     * 设置iptables开机启动/不启动 

       `chkconﬁg iptables on`

       `chkconﬁg iptables oﬀ`

     * 开放某个端⼝号` /sbin/iptables -I INPUT -p tcp --dport 3306 -j ACCEPT`





#	6.Linux上的目录

​		在 Linux 操作系统中，所有的文件和目录都被组织成以一个根节点“/”开始的倒置的树状结构

![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/1578741764.png)

​		为了方便管理和维护，Linux 系统采用了文件系统层次标准，也称为 FHS 标准，它规定了根目录下各个目录应该存在哪些类型的文件（或子目录），比如说，在 /bin 和 /sbin 目录中存放的应该是可执行文件。

| 一级目录 | 功能（作用）                                                 |
| -------- | ------------------------------------------------------------ |
| /bin/    | 存放系统命令，普通用户和 root 都可以执行。放在 /bin 下的命令在单用户模式下也可以执行 |
| /boot/   | 系统启动目录，保存与系统启动相关的文件，如内核文件和启动引导程序（grub）文件等 |
| /dev/    | 设备文件保存位置                                             |
| /etc/    | 配置文件保存位置。系统内所有采用默认安装方式（rpm 安装）的服务配置文件全部保存在此目录中，如用户信息、服务的启动脚本、常用服务的配置文件等 |
| /home/   | 普通用户的主目录（也称为家目录）。在创建用户时，每个用户要有一个默认登录和保存自己数据的位置，就是用户的主目录，所有普通用户的主目录是在 /home/ 下建立一个和用户名相同的目录。如用户 liming 的主目录就是 /home/liming |
| /lib/    | 系统调用的函数库保存位置                                     |
| /media/  | 挂载目录。系统建议用来挂载媒体设备，如软盘和光盘             |
| /mnt/    | 挂载目录。早期 Linux 中只有这一个挂载目录，并没有细分。系统建议这个目录用来挂载额外的设备，如 U 盘、移动硬盘和其他操作系统的分区 |
| /misc/   | 挂载目录。系统建议用来挂载 NFS 服务的共享目录。虽然系统准备了三个默认挂载目录 /media/、/mnt/、/misc/，但是到底在哪个目录中挂载什么设备可以由管理员自己决定。例如，笔者在接触 Linux 的时候，默认挂载目录只有 /mnt/，所以养成了在 /mnt/ 下建立不同目录挂载不同设备的习惯，如 /mnt/cdrom/ 挂载光盘、/mnt/usb/ 挂载 U 盘，都是可以的 |
| /opt/    | 第三方安装的软件保存位置。这个目录是放置和安装其他软件的位置，手工安装的源码包软件都可以安装到这个目录中。不过笔者还是习惯把软件放到 /usr/local/ 目录中，也就是说，/usr/local/ 目录也可以用来安装软件 |
| /root/   | root 的主目录。普通用户主目录在 /home/ 下，root 主目录直接在“/”下 |
| /sbin/   | 保存与系统环境设置相关的命令，只有 root 可以使用这些命令进行系统环境设置，但也有些命令可以允许普通用户查看 |
| /srv/    | 服务数据目录。一些系统服务启动之后，可以在这个目录中保存所需要的数据 |
| /tmp/    | 临时目录。系统存放临时文件的目录，在该目录下，所有用户都可以访问和写入。建议此目录中不能保存重要数据，最好每次开机都把该目录清空 |
| /var     | 目录用于存储动态数据，例如缓存、日志文件、软件运行过程中产生的文件等。通常，此目录下建议包含如表 4 所示的这些子目录。 |





#	7.Linux常用命令

1. ip addr：查询ip地址 

   ![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/1578742020.png)

2. reboot：重启电脑 

   ```
   [root@localhost /]# reboot
   ```

3. shutdown -h：现在关机

   ```
   [root@localhost /]# shutdown -h
   ```

4. pwd：打印⼯作⽬录 ，功能是显示⽤户当前所处的⼯作⽬录

    ```
   [guohangbk@localhost test2]$ pwd
   /home/guohangbk/test/test2
    ```

5. cd：进入目录或者退出目录

   ```
   [root@localhost test]# cd ..
   
   [root@localhost /]# cd /home
   [root@localhost home]# 
   ```

6. ls：list 的缩写，其主要功能是显示当前目录下的内容

   ```
   [root@localhost test]# ls
   tmp  tmp11  ttt
   ```

7. mkdir：用于创建新目录，此命令所有用户都可以使用。可以使用-p递归创建目录。

   ```
   [root@localhost test]# mkdir aass
   [root@localhost test]# ls
   aass  tmp  tmp11  ttt
   ```

8. rmdir：命令用于删除空目录，-p 选项用于递归删除空目录。

   ```
   [root@localhost test]# rmdir aass
   [root@localhost test]# ls
   tmp  tmp11  ttt
   ```

9. touch：可以用来创建文件，更重要的功能是修改文件的时间参数

   ```
   [root@localhost ttt]# touch test111
   [root@localhost ttt]# ls
   t1  test111  tmp11  tmp11.tar
   ```

10. ln：用于给文件创建链接。-s是软连接，类似快捷方式。什么都不带是硬链接。-f：强制。

    ```
    [guohangbk@localhost Desktop]$ ln -s /home/guohangbk/test/test2/asd vvv
    [guohangbk@localhost test]$ ln /home/guohangbk/test/ttt/test tmp
    ```

11. cp：主要用来复制文件和目录，同时借助某些选项，还可以实现复制整个目录。-r：递归复制

    ```
    [guohangbk@localhost test]$ cp tmp tmp11
    [guohangbk@localhost test]$ ll
    total 8
    -rw-rw-r--. 2 guohangbk guohangbk 107 Dec 17 16:21 tmp
    -rw-rw-r--. 1 guohangbk guohangbk 107 Dec 17 16:22 tmp11
    drwxrwxr-x. 2 guohangbk guohangbk  33 Dec 17 16:18 ttt
    ```

12. rm：删除命令，可以永久性地删除文件系统中指定的文件或目录。-f：强制删除，-r：递归删除

    ```
    [root@localhost test]# rm -rf ttt
    [root@localhost test]# ls
    tmp  tmp11
    ```

13. mv：可以在不同的目录之间移动文件或目录，也可以对文件和目录进行重命名。

    ```
    [root@localhost test]# mkdir test1
    [root@localhost test]# mv tmp test1
    [root@localhost test]# cd test1
    [root@localhost test1]# ls
    tmp
    ```

14. vim：文本编辑器，i进⼊插⼊模式进⾏编辑，esc进⼊命令模式，：进⼊底⾏模式，wq保存退出

    ```
    [root@localhost test1]# vim tmp
    
    zheli shi yijuhua
    我是全球最帅的男人哈哈哈
    Õµļٵİ¡
    asdddddddddddddddddddd
    hahahaha
    哈哈
    ```

15. cat：查看文件

    ```
    [root@localhost test1]# cat tmp
    zheli shi yijuhua
    我是全球最帅的男人哈哈哈
    Õµļٵİ¡
    asdddddddddddddddddddd
    hahahaha
    哈哈
    ```

16. tar：用于打包操作，后缀为.tar

```
[root@localhost ttt]# tar -cvf tmp11.tar tmp11
tmp11
[root@localhost ttt]# ll
total 20
-rw-rw-r--. 2 guohangbk guohangbk   107 Dec 17 16:21 t1
-rw-rw-r--. 1 guohangbk guohangbk   107 Dec 17 16:23 tmp11
-rw-r--r--. 1 root      root      10240 Dec 17 19:48 tmp11.tar

[root@localhost ttt]# tar -xvf tmp11.tar
tmp11
```



​		Linux命令学习：http://c.biancheng.net/view/722.html





#	8.其他命令

1. find

   在目录中查找文件   find 搜索路径 [选项] 搜索内容 -name: 按照文件名搜索； -iname: 按照文件名搜索，不区分文件名大小； -inum: 按照 inode 号搜索；

2. tail

   显示文件结尾       tail [选项] 文件名 -n 行数：从文條尾开始，显示指定行数 -f：监听文件的新増内容（ctrl+c退出）  使用echo “””>>文件进行追加

3. grep

   grep [选项] ‘搜索字符串’ 文件名 

   -a ：将 binary 文件以 text 文件的方式搜寻数据 

   -c ：计算找到 '搜寻字符串' 的次数

   -i ：忽略大小写的不同，所以大小写视为相同 

   -n ：顺便输出行号 

   -v ：反向选择，亦即显示出没有 '搜寻字符串' 内容的那一行！ 

   --color=auto ：可以将找到的关键词部分加上颜色的显示喔！ 

   Grep ‘查找字符串’ 文件名  == cat 文件名|grep ‘查找字符串’  输出|前边输出作为 管道符后边的输入  

4. wc统计字节、行数等 wc 【】 文件

   -c 统计字节数。

   -l 统计行数。

   -m 统计字符数。这个标志不能与 -c 标志一起使用。

   -w 统计字数。一个字被定义为由空白、跳格或换行字符分隔的字符串。

   -L 打印最长行的长度。 

   -help 显示帮助信息 

   --version 显示版本信息

5. df

   磁盘使用空间

6. du

   显示指定文件目录已用空间

7. free 内存使用  

   -m以M显示

8. top

   查看最耗资源的进程

9. ps 进程状态

   -A 列出所有的行程 

   -w 显示加宽可以显示较多的资讯 

   -au 显示较详细的资讯

   -aux 显示所有包含其他使用者的行程

10. kill 杀死进程

    kill –9 进程号 彻底杀死进程

11. Netstat 查看网络状态

    -a：列出所有网络状态，包括 Socket 程序； 

    -c秒数：指定每隔几秒刷新一次网络状态； 

    -n：使用 IP 地址和端口号显示，不使用域名与服务名；

    -p：显示 PID 和程序名；

    -t：显示使用 TCP 协议端口的连接状况；

    -u：显示使用 UDP 协议端口的连接状况；

    -I：仅显示监听状态的连接； 

    -r：显示路由表；

12. tracert 查看跳数 

    tracert 域名





#	9.Vim编辑器		

##	a.简述

​		Vim 是⼀个基于⽂本界⾯的编辑⼯具，使⽤简单且功能强⼤。更重要的是，Vim 是所有 Linux 发⾏版本默认的⽂本编辑器。

​		VI——VIM是VI的升级版 

​		vi / vim是Unix / Linux上常⽤的⽂本编辑器⽽且功能⾮常强⼤。只有命令，没有菜单。

​		命令⾏模式：command  mode 控制屏幕光标的移动，字符、字或⾏的删除，移动复制某区段及进⼊Insert mode下，或者 到 last 

​		插⼊模式： Insert  mode 也称为编辑⽂本模式 只有在Insert mode下，才可以做⽂字输⼊，按 「ESC」键可回到命 令⾏模式。

​		底⾏模式 last line mode将⽂件保存或退出vi，也可以设置编辑环境，如寻找字符串、列出⾏号等。     不过⼀般我们在使⽤时把vi简化成两个模式，就是将底⾏模式 （last line mode）也算⼊命令⾏模式 command mode）。



##	b.操作步骤

- 1. vim 路径+⽂件名，输⼊i/a/o进⼊编辑模式
  2. 如果想退出 vi，则需要先切换到底⾏模式
  3. 在插⼊模式下，先按ESC键，然后按下：键，则切换到底⾏模式；
  4. 如果想保存当前编辑的内容，在：后输⼊wq! ，即 :wq!  ,这样就将⽂本保存了.然后 退出了vi编 辑器。  
  5. 如果不想保存当前编辑的内容，在：后输⼊q! ，即 :q!  ,这样放弃本次编辑直接退出 了vi编辑器。
  6. 字⺟的含意义： w：write； q：quit；  !：强制指定，当多⼈同时修改⼀个⽂件，修改只读⽂件等情况⽐较有意义，因此单⼈操作 的机器中，可以省略。



##	c.常用命令说明

- **进⼊编辑模式：**

  i：在当前光标所在位置插⼊随后输⼊的⽂本，光标后的⽂本相应向右移动 

  I：在光标所在⾏的⾏⾸插⼊随后输⼊的⽂本，⾏⾸是该⾏的第⼀个⾮空⽩字符，相当于光 标移动到⾏⾸执⾏i命令 

  a：在当前光标所在位置之后插⼊随后输⼊的⽂本 

  A：在光标所在⾏的⾏尾插⼊随后输⼊的⽂本，相当于光标移动到⾏尾再执⾏a命令 

  o：在光标所在⾏的下⾯插⼊新的⼀⾏。光标停在空⾏⾸，等待输⼊⽂本

  O：在光标所在⾏的上⾯插⼊新的⼀⾏。光标停在空⾏的⾏⾸，等待输⼊⽂本



- **编辑模式下光标移动说明**

  在Vim中进⾏定位需要通过上、下、左、右⽅向键，⿏标不管⽤。还可以在命令模式中使⽤ h、j、k、l 这 4 个字符控制⽅向，分别表示向左、向下、向上、向左。

  普通模式下： 

  ctrl+b后移动⼀⻚ 

  ctrl+f前移⼀⻚

  ctrl+u后移动半⻚ 

  ctrl+d前移动半⻚

  W到光标移动的下⼀⾏头

  b移动上⼀⾏⾏头



- **普通模式下的命令：（直接进⼊或者修改状态点击esc）**

  gg到⽂本的第⼀⾏ 

  G到⽂本的后⼀⾏ 

  u:undo,取消上⼀步操作 

  ctrl+r:redo，返回取消上⼀步之前

  r：替换光标所在处的字符（点击r之后输⼊替换⽂本）

  R：从光标处开始替换，⼀直到esc结束 

  x:删除光标所在处字符

  nx：删除光标所在处n个字符 

  dd删除光标所在⾏（整⾏删除） 

  ndd删除n⾏ 

  dG删除光标所在⾏到末尾⾏的所有内容 

  D：删除光标所在处到⾏尾的内容（⾏还在只是内容没了） 

  Shift+zz：保存退出

  v:进⼊字符可视化模式（⽤于选择信息） 

  V或者shift+v进⼊⾏可视化模式（⽤于选择信息） 

  Ctrl+v进⼊块可视化模式（⽤于选择信息）

  y拷⻉ 

  p粘贴 

  yw:拷⻉光标所在到⼦字符尾部 

  yy：拷⻉整⾏ 

  查找： /查找内容

  

- **命令⾏常⽤命令：**

  ：set nu显示⾏号 

  ：set nonu 取消⾏号 

  ：n到⽂本的n⾏ 

  ：5,7d 删除指定范围的⾏

  

- **查找替换** 

  ：S/原字符/新字符/g当前⾏ 

  ：%s/…….全局替换

