#### 1.正反向代理

正向代理（Forward Proxy）： 正向代理是指代理服务器位于客户端和目标服务器之间，为客户端提供代理服务。客户端发起请求时，首先将请求发送给代理服务器，然后代理服务器再将请求发送给目标服务器，并将响应返回给客户端。客户端和目标服务器是不直接通信的。
正向代理的主要作用是隐藏客户端的真实IP地址和身份信息，保护客户端的隐私。同时，正向代理还可以过滤和缓存请求，提高访问速度，并且可以实现网络访问控制，限制特定客户端的访问权限。

举个例子来说，如果你在公司中使用正向代理访问互联网，代理服务器会代替你向外部服务器发送请求，这样你的真实IP地址和身份就得以隐藏。

反向代理（Reverse Proxy）： 反向代理是指代理服务器位于目标服务器和客户端之间，为目标服务器提供代理服务。当客户端发送请求时，请求首先到达反向代理服务器，然后由代理服务器将请求转发到目标服务器，最后将目标服务器的响应返回给客户端。客户端和目标服务器是不直接通信的。
反向代理的主要作用是为目标服务器提供负载均衡、缓存、SSL加密等服务。它可以根据负载情况将请求分发到多个后端服务器，提高系统的可扩展性和稳定性。同时，反向代理还可以隐藏目标服务器的真实IP地址和拦截恶意请求，提高服务器的安全性。

举个例子来说，当你访问一个网站时，实际上你是与反向代理服务器进行通信，代理服务器根据负载情况将请求转发到多个后端服务器上，从而实现负载均衡和提高访问速度。

总结起来，正向代理隐藏客户端的真实身份和IP地址，而反向代理隐藏了服务器的真实身份和IP地址。它们在网络通信中发挥着重要的作用，提高了隐私保护、安全性和性能方面的需求。





#### 2.nginx配置文件

<p style="color:red;height:1px">nginx -t 配置文件名 可检查nginx配置文件是否错误</p>

<p style="color:red">nginx -s reload 可以重新加载nginx

nigix主要文件路径为：

/etc/nginx/               #nginx的配置目录
/etc/nginx/nginx.conf/    #nginx的主配置文件
/usr/share/nginx/         #默认存放网页的目录置

/etc/nigix/nginx.conf配置文件内容由多个块组成，最外面的块是main，main包含Events和HTTP，HTTP包含upstream和多个Server，Server又包含多个location

main（全局设置）、server（主机设置）、upstream（负载均衡服务器设置）和 location（URL匹配特定位置的设置）。

main块设置的指令将影响其他所有设置；
server块的指令主要用于指定主机和端口；
upstream指令主要用于负载均衡，设置一系列的后端服务器；
location块用于匹配网页位置
在这四个部分当中，每个部分都包含若干指令，这些指令主要包含Nginx的主模块指令、事件模块指令、HTTP核心模块指令，同时每个部分还可以使用其他HTTP模块指令，例如Http SSL模块、HttpGzip Static模块和Http Addition模块等。

main部分设置的指令将影响其它所有部分的设置；server部分的指令主要用于指定虚拟主机域名、IP和端口；upstream的指令用于设置一系列的后端服务器，设置反向代理及后端服务器的负载均衡；location部分用于匹配网页位置（比如，根目录“/”,“/images”,等等）。

他们之间的关系式：server继承main，location继承server；upstream既不会继承指令也不会被继承。它有自己的特殊指令，不需要在其他地方的应用。

#### <span style="color:red">3.反向代理配置</span>

修改/etc/nginx/nginx.conf文件为如下格式：

user nginx;
worker_processes auto;
error_log /var/log/nginx/error.log;
pid /run/nginx.pid;

include /usr/share/nginx/modules/*.conf;

events {
    worker_connections 1024;
}

http {
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                       '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';
    access_log  /var/log/nginx/access.log  main;
    sendfile            on;
    tcp_nopush          on;
    tcp_nodelay         on;
    keepalive_timeout   65;
    types_hash_max_size 2048;
    include             /etc/nginx/mime.types;
    default_type        application/octet-stream;

include /etc/nginx/conf.d/*.conf;

​    include /etc/nginx/default.d/*.conf;

<span style="color:red">upstream web1 </span>{
        server 192.168.0.222  weight=1;        \#此处填写被代理服务器IP

​		server 192.168.0.17  weight=1;

\#ip_hash;  \#使用hash可以让一个用户一直访问同一个web页面

​        }

server{
        listen 80;

​     	<span style="color:red">  server_name www.farmsec.org;</span>

​        access_log  /var/log/nginx/farmsec.log;
​        location / {
​            root /home/web1_root;
​            <span style="color:red">proxy_pass http://web1;</span>
​            proxy_read_timeout 300;
​            proxy_connect_timeout 300;
​            proxy_redirect     off;
​            proxy_set_header   X-Forwarded-Proto $scheme;
​            proxy_set_header   Host              $http_host;
​            proxy_set_header   X-Real-IP         $remote_addr;
​            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
​        }
​    }
}



upstream模块： 定义一组服务器。 这些服务器可以监听不同的端口。 而且，监听在TCP和UNIX域套接字的服务器可以混用。

在如下配置文件中，nginx反向代理服务器代理了两个服务器(192.168.0.12和192.168.0.17)，并采用负载均衡模式。所谓负载均衡是指将负载（工作任务）进行平衡、分摊到多个操作单元上进行运行。在此场景中，可以理解为nginx服务器将来访流量平均分配给被代理的两个服务器。配置文件中weigtht参数表示权值，权值越高被分配到的几率越大，此配置文件中两个weight值都为1，即代表平均分配。

upstream web1 {
    server 192.168.0.12  weight=1;      #weigtht参数表示权值，权值越高被分配到的几率越大    
    server 192.168.0.17  weight=1;
    ip_hash;                            #负载均衡集群模式



location / {

​        root /home/web1_root;       #定义服务器的默认网站根目录位置
​        proxy_pass http://web1;     #请求转向mysvr 定义的服务器列表
​        proxy_read_timeout 300;     #连接成功后，后端服务器响应时间(代理接收超时)
​        proxy_connect_timeout 300;  #nginx跟后端服务器连接超时时间(代理连接超时)
​        proxy_redirect     off;     #代理重定向

\#后端的Web服务器可以通过X-Forwarded-For获取用户真实IP

​        proxy_set_header   X-Forwarded-Proto $scheme;
​        proxy_set_header   Host              $http_host;
​        proxy_set_header   X-Real-IP         $remote_addr;

}

#### <span style="color:red">4.捕获客户端真实ip</span>

在此架构中，后端真实服务器获取的log中，IP地址变成了反向代理IP。但在实际工作时，尤其在防御黑客入侵方面，我们更需要日志记录来访的真实IP而非代理IP。这需要在配置文件中修改一个参数实现。

在反向代理服务器上开启X-Forwarded-For

<span style="color:red">vi /etc/nginx/nginx.conf 在 location内添加以下内容</span>>

在配置文件中修改如下内容：

proxy_set_header X-Real-IP $remote_addr;
proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

查看nginx的日志（2台都看)可以看到，目前反向代理服务器和被代理（网站）服务器的日志都记录了，你的kali主机IP的访问情况。

#### <span style="color:red">5.高可用及负载均衡</span>

使用高可用性及负载均衡要安装keepalived模块

修改服务器的keepalived配置文件 vi /etc/keepalived/keepalived.conf 清空写入·如下信息

主服务器

vrrp_instance VI_1 {
    state MASTER
    interface ens3
    virtual_router_id 91
    priority 150
    advert_int 1

authentication {
    auth_type PASS
    auth_pass 1122
}

virtual_ipaddress {
    10.0.0.2
}

}

备份服务器

vrrp_instance VI_2 {
    state BACKUP
    interface ens3
    virtual_router_id 91
    priority 100
    advert_int 1

authentication {
    auth_type PASS
    auth_pass 1122
}

virtual_ipaddress {
    10.0.0.2
}

}

配置完成后关机实验







10.10.50.178    nginx   主  ok

10.10.50.229    nginx  备 ok

10.10.50.48      dns 

10.10.50.217    web 1   nginx 80 apache 81

10.10.50.218    web2    nginx 80 apache 81

10.10.50.183  

10.10.50.137   mariadb

www.dw.com 10.0.0.3

www.dz.com 10.0.0.2 





