#### 1.opensss介绍

openssh默认已经安装于centos中，无须额外的安装。相关的软件包可以通过以下命令进行查询。

```bash
dnf list |grep openssh
openssh-clients.x86_64      #客户端
openssh-server.x86_64       #服务端
openssh-askpass.x86_64      #交互式访问
```

#### 2.scp命令

scp命令用于ssh客户端与服务器之前进行文件传输的命令

命令格式如下:

 1.本地服务器把文件拷贝到远程服务器上

ssh默认是22端口如果改端口了要使用 scp -P 端口号 传输文件的时候用户要对文件或文件夹有权限

scp 本地文件路径  远程主机用户名@ip地址:拷贝到远程主机的目录 如果拷贝本地文件夹到远程主机要用scp -r

scp /etc/passwd  root@10.0.0.1:/root

2.本地服务器下载远程服务器文件

scp 远程主机用户名@ip地址:主机文件路径 下载的如果是目录要用 scp -r

scp root@10.0.0.1:/etc/passwd

3.从远程服务器1把文件拷贝到远程服务器2上面命令如下

 scp root@10.10.60.77:/etc/passwd  root@10.10.60.75:/root

####  3.配置免密码(公私钥)登录

在客户机上输入 ssh-keygen一直回车生成秘钥生成后文件在家目录下.ssh里

id_rsa为私钥 id_rsa.pub为公钥 

在服务器开启整数验证 默认是开启的若没有开启可编辑ssh文件vi /etc/ssh/sshd_config 将文本中有关第48行`PubkeyAuthentication` 和第52行`AuthorizedKeysFile`的条目修改为如下内容：

```bash
PubkeyAuthentication yes
AuthorizedKeysFile      .ssh/authorized_keys
```

去除免密登录删除服务器端root下.ssh文件

#### 4.修改ssh默认端口号

vi /etc/ssh/sshd_config 修改17行把注释去掉然后修改端口号

修改后重启sshd服务客户端使用命令 ssh -p 2222 root@10.10.50.242

#### 5,配置ssh日志

ssh服务的日志存储于`/var/log/secure`。  `tail -f /var/log/secure` 大家可以自由定制所想查看的日志，例如模拟登录失败，登录成功等不同方式，以观察日志的不同变化。 状态:

- Failed password 为密码错误
- Accept  password 为登录成功
- disconnected by user 为断开链接

所需命令：

> vi /etc/ssh/sshd_config
>
> ```
> #SyslogFacility AUTH
> 
> SyslogFacility   AUTHPRIV
> 
> LogLevel DEBUG
> ```
>
> systemctl restart sshd.service
>
> tail -f /var/log/secure

在此实验中，我们将学习在ssh配置文件中配置ssh日志。

`vi /etc/ssh/sshd_config`将光标定位到约36行可以查看到如下信息。

`SyslogFacility AUTHPRIV` 表示当有人使用ssh登录系统时，ssh会记录信息，记录类型为AUTHPRIV。 `#LogLevel INFO`表示设置记录sshd日志信息的级别。

`SyslogFacility`一项，规定了服务日志的性质，也规定了服务日志存储的默认路径。

如：`AUTHPRIV` 表达了日志记录的是包含敏感信息的用户身份验证消息，默认存储于`/var/log/secure`。

`AUTH` 则表达了日志记录的是不包含敏感信息的用户身份验证消息，同样默认存储于`/var/log/secure`。

ssh配置文件的这一字段一般是不需要修改的。在LInux系统的其他服务配置文件中，你可能会遇见`SyslogFacility`设置为其他状态的情况，可以通过下表了解其含义。

| 设置         | 描述                                                | 默认日志文件                    |
| ------------ | --------------------------------------------------- | ------------------------------- |
| **local0**   | BIG-IP 特定消息                                     | **/var/log/ltm**                |
| **local1**   | EM 特定消息 APM 特定消息                            | **/var/log/em /var/log/apm**    |
| **loacl2**   | GTM 和链路控制器特定消息                            | **/var/log/gtm**                |
| **local3**   | ASM 特定消息                                        | **/var/log/asm**                |
| **loacl4**   | ITCM 门户和服务器 (iControl) 特定消息               | **/var/log/ltm**                |
| **loacl5**   | 包过滤特定消息                                      | **/var/log/pktfilter**          |
| **loacl6**   | HTTPD 特定消息                                      | **/var/log/httpd/httpd_errors** |
| **loacl7**   | Linux 特定的引导消息                                | **/var/log/boot.log**           |
| **cron**     | 与**cron**进程相关的消息                            | **/var/log/cron**               |
| **daemon**   | 与系统守护进程相关的消息（包括**named**和**ntpd**） | **/var/log/daemon.log**         |
| **kern**     | 内核消息                                            | **/var/log/kern.log**           |
| **mail**     | 邮件系统消息                                        | **/var/log/maillog**            |
| **auth**     | 不包含敏感信息的用户身份验证消息                    | **/var/log/secure**             |
| **authpriv** | 包含敏感信息的用户身份验证消息                      | **/var/log/secure**             |
| **user**     | 与用户进程相关的消息                                | **/var/log/user.log**           |

`#LogLevel INFO`表示设置记录sshd日志信息的级别。可以理解为设置了日志的详细程度。

`INFO`级别代表了日志会报告大部分有用信息，是较为详细的日志等级，除此外，还有其他等级：

| 等级        | 描述                             | 冗长度   |
| ----------- | -------------------------------- | -------- |
| **emerg**   | 紧急系统紧急信息                 | 最低限度 |
| **alert**   | 需要管理员干预的严重错误         | 低的     |
| **crit**    | 严重错误，包括硬件和文件系统故障 | 低的     |
| **err**     | 非关键但可能非常重要的错误消息   | 低的     |
| **warning** | 至少应该记录以供审查的警告消息   | 中等的   |
| **notice**  | 包含有用但可能会被忽略的消息     | 中等的   |
| **info**    | 包含有用但可能会被忽略的消息     | 高的     |
| **debug**   | i                                | 最大值   |

i你可以将此条注释取消，将其修改为`#LogLevel DEBUG`，重启ssh服务（systemctl restart sshd.service）,观察修改前后日志（tail -f /var/log/secure）详细度的变化。

#### 6.禁止root登录与配置超时时间

##### 1.禁止root登录配置如下

vi /etc/ssh/sshd_config

```
PermitRootLogin no
```

配置完后重启服务,再使用root连接客户端会显示权限拒绝

##### 2.配置ssh长时间不操作自动断开连接

所需命令：

> vi /etc/ssh/sshd_config
>
> ```
> LoginGraceTime 2m
> 
> PermitEmptyPasswords no
> ```
>
> systemctl restart sshd.service
>
> vi /etc/profile
>
> ```
> export TMOUT=10
> ```
>
> source /etc/profile         source为刷新文件将配置应用
>
> 约在配置文件40行的`#LoginGraceTime 2m`，设置了指定时间内没有成功登录，将会断开连接，若无单位则默认时间为秒。图中默认为2分钟，如需调整可取消注释，调整为自己想要设定的值。
>
> 在`/etc/profile`这个配置文件的末尾加上
>
> ```bash
> export TMOUT=10      #单位是秒
> ```
>
> 执行`source /etc/profile`命令，使配置文件生效可以看到，当命令终端超过10秒无人操作，ssh连接自动断开了。

#### 7.配置空口令登录与密码策略

##### 1.空口令登录 

删除用户的密码后才能空口令登录

配置文件`/etc/ssh/sshd_config`64行的`PermitEmptyPasswords no`，默认设置禁止空口令登录，将其取消掉`#`注释并将`no`改为`yes`，更改完后记得**重启**sshd服务使配置文件生效

##### 2.linux用户密码策略

> vi /etc/login.defs
>
> ```
> PASS_MAX_DAYS 99
> PASS_MIN_DAYS  1
> PASS_MIN_LEN   8
> PASS_WARN_AGE  1
> ```
>
> vi /etc/security/pwquality.conf
>
> ```
> minlen = 8
> minclass = 4
> maxrepeat = 2
> maxclassrepeat = 2
> lcredi = 1
> ucredit = 1
> dcredit = 1
> ocredit = 1
> ```

启用密码策略有两个配置文件： 第一个是`/etc/login.defs` 修改文件里的策略：`vi /etc/login.defs`

```bash
PASS_MAX_DAYS 99      #密码99天过期
PASS_MIN_DAYS  1      #修改密码最小间隔为1天
PASS_MIN_LEN   8      #密码最短长度为8
PASS_WARN_AGE  1      #密码过期前1天内通知用户
```

第二个配置文件为：`/etc/security/pwquality.conf`

修改文件里的策略：`vi /etc/security/pwquality.conf`

```bash
# "N" 处应填写数字
minlen=N              #定义用户密码的最小长度； 
minclass=N            #定义密码必须满足同时有几种字符；
maxrepeat=N           #定义密码中允许几个连续相同的字符；    
maxclassrepeat=N      #定义用户密码连续字符的最大数目；
lcredit=N             #定义用户密码中必须包含多少个小写字母；
ucredit=N             #定义用户密码中必须包含多少个大写字母；1
dcredit=N             #定义用户密码中必须包含多少个数字；1
ocredit=N             #定义用户密码中必须包含多少个特殊字符（除数字、字母之外）；其中 = 1表示，至少有一个
```

#### 8.ssh访问速度调优

所需命令：

> vi /etc/ssh/sshd_config
>
> ```
> GSSAPIAuthentication no
> UseDNS no
> ```
>
> systemctl restart sshd.service

将此两行改为no可解决ssh链接慢的问题

```bash
GSSAPIAuthentication no      #关闭GSSAPI认证
UseDNS no                    #关闭DNS解析
```

一般 SSH 依次进行的认证方法的是 publickey, gssapi-keyex, gssapi-with-mic, password。 一般用户只使用 password 认证方式，但前面 3 个认证过程系统还是会尝试，这就浪费时间了，也就造成 SSH 登录慢。GSSAPI  主要是基于 Kerberos 的，因此要解决这个问题也就变成要系统配置有 Kerberos， 一般用户是没有配置 Kerberos的。