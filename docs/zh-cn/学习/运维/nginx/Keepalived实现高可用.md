#	Keepalived实现高可用



#	1.Keepalived简介

Keepalived是基于vrrp协议的一款高可用软件。Keepailived有一台主服务器和多台备份服务器，在主服务器和备份服务器上面部署相同的服务配置，使用一个虚拟IP地址对外提供服务，当主服务器出现故障时，虚拟IP地址会**自动漂移**到备份服务器。

VRRP（Virtual Router Redundancy Protocol，虚拟路由器冗余协议），VRRP是为了解决静态路由的高可用。VRRP的基本架构 虚拟路由器由多个路由器组成，每个路由器都有各自的IP和共同的VRID(0-255)，其中一个VRRP路由器通过竞选成为MASTER，占有VIP，对外提供路由服务，其他成为BACKUP，MASTER以IP组播（组播地址：224.0.0.18）形式发送VRRP协议包，与BACKUP保持心跳连接，若MASTER不可用（或BACKUP接收不到VRRP协议包），则BACKUP通过竞选产生新的MASTER并继续对外提供路由服务，从而实现高可用。





#	2.应用的高可用（HA）问题

目前的架构中，nginx可以实现tomcat应用的负载均衡，我们已经能通过Nginx来实现Tomcat应用的负载均衡，但是单个的Nginx会存在单点隐患，如果Nginx挂掉，那么全部的Tomcat应用都将变得不可用，所以实现Nginx的高可用是必不可少的一步。

##	2.1 Keepalived实现Nginx高可用

Keepalived的作用是检测服务器的状态，如果有一台服务器宕机，或工作出现故障，Keepalived将检测到，并将有故障的服务器从系统中剔除，同时使用其他服务器代替该服务器的工作，当服务器工作正常后Keepalived自动将服务器加入到服务器群中，**这些工作全部自动完成，不需要人工干涉**，需要人工做的只是修复故障的服务器。类似软件还有（heartbeat，vcs）

![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/20200123111503.png)



##	2.2 Keepalived安装步骤

###	2.2.1 Keepalived如何实现虚拟IP映射的

1. 安装软件

   ```
   yum -y install keepalived
   ```

   

2. 修改主机配置文件

   ```
   ! Configuration File for keepalived
   
   global_defs {
     router_id LVS_DEVEL #id要唯一
   }
   
   vrrp_instance VI_1 {
      state MASTER #角色是master
      interface ens33
     virtual_router_id 66 #master 与backeup保持一致
     priority 100 #master 大于backeup
     advert_int 1
     authentication {
         auth_type PASS
         auth_pass 1111
     }
     virtual_ipaddress {
          192.168.66.16 #master 与backeup保持一致
     }
   }
   ```

   

3. 修改备机配置文件

   ```
   ! Configuration File for keepalived
   global_defs {
     router_id LVS_DEVEL_UP
   }
   
   vrrp_instance VI_1 {
      state BACKUP #角色是master
      interface ens33
     virtual_router_id 66 #master 与backeup保持一致
     priority 50 #
     advert_int 1
     authentication {
         auth_type PASS
         auth_pass 1111
     }
     virtual_ipaddress {
          192.168.66.16 #master与backup保持一致
     }
   }
   ```

   

4. 分别在2台服务器上启用keepalived

   ```
   service keepalived start
   ```

   

5. 验证 虚拟IP漂移

   + 在master上输入ip add 查看目前的网卡设备ens33出现2个ip地址
   + 通过arp -a ip地址`arp -a 192.168.66.16`地址解析协议，即ARP（Address Resolution Protocol），是根据IP地址获取物理地址的一个TCP/IP协议。
   + 关闭主服务器验证地址漂移

重启keepalived服务器

```
service keepalived restart  //重启keepalived
service keepalived status //查看keepalived状态
```



###	2.2.2 Keepalive+Nginx实现高可用

1. 修改主机配置文件，查看keepalived的信息

   ```
   ! Configuration File for keepalived
   ​
   global_defs {
   #服务器的全局属性作为当前服务器的标识，每台服务器要唯一
     router_id LVS_DEVEL
     script_user root
   }
   #声明 nginx 监控脚本
   vrrp_script chk_nginx {
       script "/etc/keepalived/check_nginx.sh" #脚本需要授权才能执行
       interval 2 #每2s检测一次
       weight -2 #检测失败（脚本返回非0）则优先级 -2
       fall 3 # 检测连续 2 次失败才算确定是真失败。
       rise 1 # 检测 1 次成功就算成功。但不修改优先级
   }
   vrrp_instance VI_1 {
     state MASTER #指定keepalived的角色，MASTER为主服务器，BACKUP为备用服务器
     interface ens33
     virtual_router_id 66 #MASTER 与BACKUP要保持一致
     priority 100 #优先级越大越高，主的必须大于备用的
     advert_int 1 #心跳间隔，MASTER会每隔1秒发送报文通知其他机器，自己还活着
     authentication {
         auth_type PASS
         auth_pass 1111
     }
     virtual_ipaddress {
          192.168.66.16 #设置主DR的虚拟IP，可以设置多个，但必须每行一个
     }
     track_script {
         chk_nginx
     }
   }
   ```

   

2. 修改备机配置文件

   ```
   ! Configuration File for keepalived
   ​
   global_defs {
   #服务器的全局属性作为当前服务器的标识，每台服务器要唯一
     router_id LVS_DEVEL_BACKUP
     script_user root
   }
   vrrp_script chk_nginx {
         script "/etc/keepalived/check_nginx.sh"
         interval 2
         weight -2 #检测失败（脚本返回非0）则优先级 -2
       fall 3 # 检测连续 2 次失败才算确定是真失败。
       rise 1 # 检测 1 次成功就算成功。但不修改优先级
   }
   vrrp_instance VI_1 {
     state BACKUP #指定keepalived的角色，MASTER为主服务器，BACKUP为备用服务器
     interface ens33
     virtual_router_id 66 #MASTER 与BACKUP要保持一致
     priority 50 #优先级越大越高，主的必须大于别用的
     advert_int 1 #心跳间隔，MASTER会每隔1秒发送报文通知其他机器，自己还活着
     authentication {
         auth_type PASS
         auth_pass 1111
     }
     virtual_ipaddress {
          192.168.66.16 #设置主DR的虚拟IP，可以设置多个，但必须每行一个
     }
     track_script {
         chk_nginx
     }
   }
   ```

   

3. 配置脚本

   ```
   #!/bin/bash
   A=`ps -C nginx --no-header |wc -l`
   if [ $A -eq 0 ];then
   /usr/local/nginx-1.17.3/sbin/nginx
      if [ `ps -C nginx --no-header |wc -l` -eq 0 ];then
          killall keepalived
      fi
   fi
   ```

   

4. 测试高可用

- - 关闭主服务器发现服务对外正常
  - 关闭nginx发现服务对外正常



#	3.Nginx反向代理实现动静分离

##	3.1 动静分离是什么？

在弄清动静分离之前，我们要先明白什么是动，什么是静。

在Web开发中，通常来说，**动态资源其实就是指那些后台资源**，**而静态资源就是指HTML，JavaScript，CSS，img等文件。**

一般来说，都需要将动态资源和静态资源分开，将静态资源部署在Nginx上，当一个请求来的时候，如果是静态资源的请求，就直接到nginx配置的静态资源目录下面获取资源，如果是动态资源的请求，nginx利用反向代理的原理，把请求转发给后台应用去处理，从而实现动静分离。

在使用前后端分离之后，可以很大程度的提升静态资源的访问速度。



##	3.2 对高并发处理的操作

+ 负载均衡
+ 动静分离：使用Nginx、CDN
+ 缓存：以空间换时间，提高系统效率
+ 限流：流量控制
+ 降级：服务降载



##	3.3 nginx动静分离配置文件（nginx.conf）的配置

- root 后面的目录要存在
- 此配置文件的首行#user  nobody; 改为 user root;(解决权限问题)

```
user root;
#gzip on;
  upstream tServer{
      server 192.168.66.161:8001;
      server 192.168.66.161:8002;
}
  server {
      listen       80;
      server_name  192.168.66.161;
       
       #拦截后台请求
      location / {
          # root   html;
          # index index.html index.htm;
          #proxy_pass http://www.baid.com;
          proxy_pass http://tServer;
      }
       #~ 开头表示区分大小写的正则匹配 \.（对点转义）
      location ~ \.(html|htm|gif|js|css|txt|ico|png|jgp|jpeg|eot|svg|ttf|woff) {
            root /home/ftp;
}
```

​	访问的时候直接访问的是 192.168.66.162/dem.txt (/home/ftp下面要有这个文件)





小知识：跨服务器传输文件的命令

```
[root@localhost java]# scp springbootstarter-0.0.1-SNAPSHOT.jar root@192.168.77.130:/usr/local/java
```



<font color="red">本节可能出现的问题</font>

出现这个提示会导致linux下的keepalived不能启动

![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/20200118223355.png)

解决：关闭Linux内核保护机制

```
[root@localhost keepalived]# setenforce 0
```