#	1.Nginx简介

##	1.1 Nginx是什么？

**Nginx (“engine x”) 是一个高性能的HTTP和反向代理web服务器**，同时也提供了IMAP/POP3/SMTP服务。**Nginx是一款轻量级的Web 服务器/反向代理服务器及电子邮件（IMAP/POP3）代理服务器**，在BSD-like 协议下发行。其特点是占有内存少，并发能力强，事实上nginx的并发能力确实在同类型的网页服务器中表现较好，中国大陆使用nginx网站用户有：百度、京东、新浪、网易、腾讯、淘宝等。



##	1.2 Nginx作用：

- - 反向代理

  - 负载均衡

  - 动静分离：将静态资源缓存到nginx服务器。请求到达nginx后，动态资源代理到tomcat，静态资源直接从nginx获取。

    

## 1.3 什么是代理？		

客户机发送请求时，不会直接到达目标主机，先发给代理服务器，由代理服务器发送给目标服务器。

**代理服务器的好处**：

- - 起到防火墙的作用。无法直接访问真正的服务器
  - 起到缓存的作用，加速访问速度。

Nginx代理分为正向代理和反向代理。



##	1.4 正向代理

+ **场景:**
  A向B借钱-->B不借-->A发现C和B关系很好(A和C关系很好)-->A拜托C向B借钱(不要说是我借的)-->C就找B借钱-->B把钱借给C-->C把钱给A-->B并不知道谁真正在向自己借钱-->A达到了自己的目的
+ **定义:**
  客户端向真实的服务器端发送请求，但是出于某种原因无法向真实的客户端发送请求，客户端就找到代理服务器，把请求发送给代理服务器，再由代理服务器把请求发送给真实的服务器，真实服务器并不知道具体访问我的客户端是谁(真实服务器看到的访问自己的是代理，并不是真实的客户端)
+ **特点:**
  正向代理是和客户端在一起的，所有的配置都必须在客户端完成，真实的服务器端并不知道真实的客户端是谁
  必须知道代理服务器的ip和端口，用户能够明显感知到代理服务器的存在。



##	1.5 反向代理

+ **场景:**
  话费咨询--->10086/10010/10001--->移动公司的总机-->人工操作-->总机会转接到人工-->打电话的人不知道具体谁接听电话-->达到了自己想要的结果
+ **定义:**
  客户端向服务器端发送请求(服务器端是一个集群(4台服务器))，客户端并不知道具体访问哪一台服务器，客户端的请求就会被代理服务器所拦截，再由代理服务器把请求转交给集群中的某一个真实服务器，真实服务器最终把结果响应给代理服务器，代理服务器再把结果返回给客户端，客户端并不知道具体请求的服务器是真实服务器还是代理服务器。
+ **特点：**
  反向代理是和服务器端在一起的，所有的配置都必须要在服务器端完成，客户端并不知道真实的服务器是谁(客户端并不知道自己请求的是代理服务器还是真实服务器)。不需要知道代理服务器的ip和端口，用户无法感知到代理服务器的存在。一般情况下反向代理服务器跟真实服务器在一起。







#	2.Nginx在Linux安装步骤

1. 把安装包拷到指定目录下，解压后安装

   ``````
   tar -zxvf nginx-1.17.3.tar.gz
   ``````

2. 输入命令下载gcc

   ``````
   yum -y install gcc
   ``````

3. 如果报错以下信息，表示缺少nginx需要的配置包 ，安装pcre，查看软件是否安装使用命令 yum info gcc：
   ./configure: error: the HTTP rewrite module requires the PCRE library.You can either disable the module by using --without-http_rewrite_moduleoption, or install the PCRE library into the system, or build the PCRE librarystatically from the source with nginx by using --with-pcre=<path>option.

4. 安装nginx所需要的配置包，PCRE 作用是让 Nginx 支持 Rewrite 功能。

   ``````
   yum -y install pcre-devel openssl openssl-devel
   ``````

5. 在nginx的目录中，创建快捷方式 **--prefix指定编译路径**,目录先提前创建好

   ``````
   . ./configure --prefix=/usr/local/nginx-1.17.3
   ``````

   看到以下信息说明创建成功: 				

   ​				Configuration summary

   ​				using system PCRE library

      			 OpenSSL library is not used

   ​				using system zlib library

6. 编译nginx

   还需要在nginx的解压目录下继续执行

   ```
   make && make install
   ```

   ```
   看到以下信息说明编译成功:
   make[1]: Leaving directory `/home/anne/workspace/nginx-1.17.3'
   ```

7. 启动nginx

   不要在nginx的解压包目录操作，需要在快捷方式的目录执行(/usr/local/nginx-1.17.3/sbin)

   ```
   ./nginx
   如果没有报错，说明启动成功
   ```

8. 使用浏览器检测

   在地址栏输入ip地址出现如下界面就是配置成功

   ![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/clipboard.png)

9. Nginx常见命令

   ```
   启动nginx     ./sbin/nginx
   停止nginx      ./sbin/nginx -s stop  或者   ./sbin/nginx -s quit
   重载配置  ./sbin/nginx -s reload(平滑重启)  service nginx reload 
   重载指定配置文件 ./sbin/nginx -c /usr/local/nginx/conf/nginx.conf
   查看nginx版本 ./sbin/nginx -v
   检查配置文件是否正确 ./sbin/nginx -t
   显示帮助信息 ./sbin/nginx -h
   ```

10. nginx的master-worker模式；查看nginx的 端口状态

``````
[root@localhost~]# ps aux | grep nginx
root       1574  0.0  0.0  20508   616 ?        Ss   16:48   0:00 nginx: master process ./sbin/nginx
nobody     1575  0.0  0.1  23044  1392 ?        S    16:48   0:00 nginx: worker process
``````

```
netstat -tnlup | grep nginx
```

每一个Worker进程都维护一个线程（避免线程切换），处理连接和请求；注意Worker进程的个数由配置文件决定，一般和CPU个数相关（有利于进程切换），配置几个就有几个Worker进程。







#	3.Nginx实战--反向代理以及负载均衡

##	3.1 Nginx 实现反向代理

​	反向代理到百度（42 line） 在主机或者虚拟机访问 192.168.66.161 会跳转到百度

```
//nginx.conf
    server {
      listen       80;
      server_name localhost;

      location / {
          #root   html;
          #index index.html index.htm;
          proxy_pass http://www.baidu.com;
      }    
  }
```

总结：
	其实反向代理很好的保证真实服务器的安全性，也就是说隐藏了真实服务器的地址！！！
	客户端可以通过请求代理服务器的形式直接访问到真实服务器，客户端毫无察觉
	反向代理可以让服务器变得更安全

其他相关配置：
		allow:允许 (白名单)
		deny:(forbiiden)禁止（黑名单）
		禁止IP为192.168.23.169的客户端访问
		所有的配置都必须要在location /{}中完成
		<font color="red">所有的允许和禁止都必须要配置在proxy_pass的上面！！！</font>

```
location /{
    deny 192.168.66.1;
    proxy_pass http://www.baidu.com;
}

location /{
    deny all;
    proxy_pass http://www.baidu.com;
}
```

![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/20200118221138.png)

扩展知识：Http状态码

| 400  | Bad Request      | 客户端请求的语法错误，服务器无法理解                         |
| ---- | ---------------- | ------------------------------------------------------------ |
| 401  | Unauthorized     | 请求要求用户的身份认证                                       |
| 402  | Payment Required | 保留，将来使用                                               |
| 403  | Forbidden        | 服务器理解请求客户端的请求，但是拒绝执行此请求               |
| 404  | Not Found        | 服务器无法根据客户端的请求找到资源（网页）。通过此代码，网站设计人员可设置"您所请求的资源无法找到"的个性页面 |





##	3.2 Nginx实现负载均衡讲解

1. **什么是负载均衡**

   负载均衡通过反向代理实现，只是反向代理的一部分
   什么是负载
   客户端向服务器发送的请求称之为服务器的负载
   什么是负载均衡
   把客户端发送的请求按照某种规则分配到多台服务器上称之为负载均衡

   

2. **负载均衡配置(反向代理到tomcat服务，实现负载均衡)**

   a.传文件到服务器
   b.修改配置文件，配置负载均衡组（上游服务器）

   ```
   [root@localhost sbin]# vim ../conf/nginx.conf
   
       #负载均衡组
       upstream tServer{
           server 192.168.77.129:8001;
           server 192.168.77.129:8002;
       }
   
       #gzip  on;
   
       server {
           listen       80;
           server_name  localhost;
   
           #charset koi8-r;
   
           #access_log  logs/host.access.log  main;
   
           location / {
               root   html;
               index  index.html index.htm;
               proxy_pass http://tServer;
            }
       }
   ```

   c.启动程序命令：
   	指定端口号启动程序

   ```
   java -jar 1104-springboot-0.0.1-SNAPSHOT.jar --server.port=8002
   ```

   d.平滑重启nginx服务

   ```
   ./sbin/nginx -s reload
   ```

   e.测试访问

   ```
   http://192.168.77.129:8002/starter
   ```

   

3. **负载均衡策略（算法）**

   a.轮询 （round robin）

   轮询是upstream的默认分配方式，即每个请求按照时间顺序轮流分配到不同的后端服务器，如果某个后端服务器down掉后，能自动剔除。

   ```
   upstream tServer {
       server 192.168.66.161:8001;
       server 192.168.66.161:8002;
   }
   ```

   <font color="red">刷新六次</font>

   ![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/20200118221252.png)

   

   b.weight （能者多劳，物尽其用）

   轮询的加强版，即可以指定轮询比率，weight和访问几率成正比，主要应用于后端服务器异质的场景下。

   ```
   upstream tServer {
       server 192.168.66.161:8001 weight=2;
       server 192.168.66.161:8002 weight=8;
   }
   ```

   默认的权重weight=1
   	weight默认是没有上限的，可以任意配置
   	weight最小也只能为1，换句话说weight的值必须要大于等于1
   	weight必须要是整数，不能出现小数!!!!

   <font color="red">刷新十次</font>

   ![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/20200118220401.png)

   

   c.ip_hash 

   每个请求按照访问ip（即Nginx的前置服务器或者客户端IP）的hash结果分配，这样每个访客会固定访问一个后端服务器，可以解决session一致问题。

   ```
   upstream tServer {
       ip_hash;
   	server 192.168.66.161:8001;
       server 192.168.66.161:8002;
   }
   ```

   <font color="red">刷新五次</font>

   ![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/20200118220607.png)

   

   d.fair(第三方)

   按照后端服务器的响应时间来分配请求，响应时间短的优先分配。Nginx本身不支持fair，如果需要这种调度算法，则必须安装upstream_fair模块。

   

   e.url_hash(第三方)

   按照访问url的hash结果来分配请求，每个固定的url访问同一个后端服务器，Nginx本身不支持url_hash，如果需要这种调度算法，则必须安装Nginx的hash软件包。

   

   

4. **在upstream模块中，可以通过server命令指定后端服务器的IP地址和端口，同时还可以设置每台后端服务器在负载均衡调度中的状态，常用的状态有以下几种：**

   a.down(此服务器不参加负载)，暂时让down的服务器脱离集群，可以在升级系统的时候依次给部分服务器down。

```
upstream tServer{
	server 192.168.66.161:8001 down;
    server 192.168.66.161:8002;
}
```

​		<font color="red">第一台机器宕机，五次请求都在第二台机器</font>

![](https://raw.githubusercontent.com/guohangbk/picBed/master/img/20200118220939.png)



​		b.backup(备用)

​		备用服务器:
​			备胎！平时不用，但是主服务器宕机的时候，备用服务器替代主服务器
​			一旦主服务器启动后，备用服务器就会退位

```
upstream tServer{
    server 192.168.66.161:8001 backup;
    server 192.168.66.161:8002;
}
```



​		c.max_fails：

​		允许请求的失败次数，默认为1，配合fail_timeout一起使用



​		d.fail_timeout：

​		经历max_fails次失败后，暂停服务的时间，默认为10s（某个server连接失败了max_fails次，则nginx会认为		该server不工作了。同时，在接下来的 fail_timeout时间内，nginx不再将请求分发给失效的server。）

```
upstream whsirserver {
	server 192.168.66.161:8001 weight=5 max_fails=3 fail_timeout=20s;
	server 192.168.66.161:8002 weight=1 max_fails=3 fail_timeout=20s;
}
```



