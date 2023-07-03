#### 1.apache配置文件

Apache主要目录：

/etc/httpd/conf/httpd.conf   #apache的主配置文件
/etc/httpd/                  #apache配置文件的目录
/var/www/                    #apache默认存放网页的目录

/etc/httpd/conf/httpd.conf文件详解：

 ServerRoot "/etc/httpd"               #定义Apache的配置文件目录
 Listen 80                                 #定义Apache的端口
  User apache                               #定义启动用户
  Group apache                              #定义启动用户组
 ServerAdmin root@localhost                #定义管理员邮箱
#ServerName www.example.com:80            #定义服务器的域名
DocumentRoot "/var/www/html"              #定义网页目录
DirectoryIndex index.html                 #定义默认首页文件

#### 2.虚拟主机配置

##### 1.每个个ip多个站点

<VirtualHost *:80>

This first-listed virtual host is also the default for *:80

​    ServerName www.example.com
​    ServerAlias example.com 
​    DocumentRoot "/www/domain"
\</VirtualHost>

<VirtualHost *:80>
    ServerName other.example.com
    DocumentRoot "/www/otherdomain"
\</VirtualHost>

##### 2.每个ip1个站点

<VirtualHost 172.20.30.40:80>
    ServerAdmin webmaster@www1.example.com
    DocumentRoot "/www/vhosts/www1"
    ServerName www1.example.com
    ErrorLog "/www/logs/www1/error_log"
    CustomLog "/www/logs/www1/access_log" combined
\</VirtualHost>

<VirtualHost 172.20.30.50:80>
    ServerAdmin webmaster@www2.example.org
    DocumentRoot "/www/vhosts/www2"
    ServerName www2.example.org
    ErrorLog "/www/logs/www2/error_log"
    CustomLog "/www/logs/www2/access_log" combined
\</VirtualHost>

#### 3.apache日志

利用tail -f /var/log/httpd/*监控apache日志。

当我们安装并启动Apache后，Apache会自动生成两个日志文件，在linux系统中他们保存在/var/log/httpd/下，这两个日志文件分别是访问日志access_log和错误日志error_log。如果使用 SSL 服务的话，还可能存在 ssl_access_log和ssl_error_log 和 ssl_request_log 三种日志文件。

访问日志access_log记录了所有对Web服务器的访问活动，下面是访问日志access_log中的一个标准记录

192.168.10.110 - - [28/Mar/2022:08:29:09 +0800] "GET /login.php HTTP/1.1" 200 1415 "-" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/100.0.4896.60 Safari/537.36"
日志字段所代表的内容如下：

远程主机IP：表明访问网站的是谁
空白(E-mail)：为了避免用户的邮箱被垃圾邮件骚扰，第二项就用“-”取代了
空白(登录名)：用于记录浏览者进行身份验证时提供的名字。
请求时间：用方括号包围，而且采用“公用日志格式”或者“标准英文格式”。 时间信息最后的“+0800”表示服务器所处时区位于UTC之后的8小时。
方法+资源+协议：服务器收到的是一个什么样的请求。该项信息的典型格式是“METHOD RESOURCE PROTOCOL”，即“方法 资源 协议”。
状态码：请求是否成功，或者遇到了什么样的错误。大多数时候，这项值是200，它表示服务器已经成功地响应浏览器的请求，一切正常
发送字节数：表示发送给客户端的总字节数。它告诉我们传输是否被打断（该数值是否和文件的大小相同）。把日志记录中的这些值加起来就可以得知服务器在一天、一周或者一月内发送了多少数据。
UA信息：访问者的user-agent信息。

#### 4.apache安全实例

隐藏Apache的版本号及其它敏感信息其中有两个重要的指令：ServerTokens、ServerSignature

ServerTokens:

它决定了发送回客户端的服务器响应头字段是否包含服务器操作系统类型的描述和有关已启用的 Apache 模块的信息。编辑http主配置文件添加下面一种

ServerTokens Prod   #服务器会发送(比如)： Server: Apache
ServerTokens Major             #服务器会发送(比如)： Server: Apache/2
ServerTokens Minor             #服务器会发送(比如)： Server: Apache/2.0
ServerTokens Min[imal]         #服务器会发送(比如)： Server: Apache/2.0.41
ServerTokens OS                #服务器会发送(比如)： Server: Apache/2.0.41 (Unix)
ServerTokens Full (或未指定)    #服务器会发送(比如)： Server: Apache/2.0.41 (Unix) PHP/4.2.2 MyMod/1.2

此设置将作用于整个服务器，而且不能用在虚拟主机的配置段中。

ServerSignature:

这允许在服务器生成的文档（如错误消息、modproxy 的 ftp 目录列表、modinfo 输出等等）下添加一个显示服务器名称和版本号的页脚行。 它有三个可能的值：

 On - 允许在服务器生成的文档中添加尾部页脚行， Off - 禁用页脚行 EMail - 创建一个 “mailto:” 引用；用于将邮件发送到所引用文档的 ServerAdmin。

设置前使用下面命令查看服务端返回Server信息:

curl http://192.168.0.239 -i