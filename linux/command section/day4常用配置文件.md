
### 1.常用配置文件

1. /etc/passwd文件保存了系统中所有欧农户和组的重要的信息每行有7个字段以:分割每个字段代表的含义不同这里以root这一行示例     

root : x : 0 : 0 : root : /root : /bin/bash

​	第一个字段为:用户名名称

​	第二个字段为:密码表示在早期unix中这里保存加密后的密码现在密码保存在 /etc/shadow下

​	第三个字段为用户的uid

​	第四个字段为用户的gid 也就是组id

​	第五个字段为用户的说明或备注

​	第六个的字段为用户的家目录

​	第七个字段为用户的解释器,也可以理解为登陆之后的权限

2. /etc/shadow 文件是保存用户密码同样以:为分隔符这里以root举例

   root:$y$j9T$SLYEBgY.p//v9aZH/njcO0$y2zsJNz.bzF/7cAisd//4jL8yBLcai1oMr.tigs/uR.:19528:0:99999:7:::

   第一个字段为用户名称

   第二个字段为用户加密过的密码没有密码的用户则是\*或!!是不能登录的

   第三个字段为密码何时修改过的,这里显示的数字是unix时间戳的起始时间1970年1月1日如果数字为10就是1970年1月1日后的第10天

   第四个字段为两次修改密码的间隔如果是5那么密码修改后5天之内不能修改,改为0则随时都可以修改

   第五个字段为密码的有效期时间是从第三个字段开始算的

   第六个字段为密码到期前警告的天数 字段为7则密码到期前第7天警告

   第七个字段为密码的宽限天数0天就立马失效 3就是3天后失效,-1则是永不失效

   第八个字段为用户的失效时间同样以时间戳表示

   第九个字段为预留字段暂时没有任何功能

3. /etc/group文件为存放linux用户组信息

   ​			root : x : 0 :

   ​		第一个字段为:用户组的名称

   ​		第二个字段为用户组密码

   ​		第三个字段为组gid

   ​		第四个字段为用户列表 本字段可以为空

   4./etc/gshadow文件存放组用户的密码信息分为四个字段

   root : : :

      1-4 分别为 组名：加密密码：组管理员：组附加用户列表

### 2.用户与组命令

   1.useradd 命令用于添加用户
         建立uid为0的用户命令为 userad -u -o 0 aaa      -u为指定uid -o 代表重复使用uid
   2.userdel命令删除用户 userdel 用户名
   3. usermod命令用于更改用户和组
               -l<帐号名称>           #修改用户帐号名称。usermod fesc1 -l fsec2
              -c<备注>              #修改用户帐号的备注文字。
              -g<群组>              #修改用户所属的群组。
              -G<群组>              #修改用户所属的附加群组。
              -d<登入目录>           #修改用户登入时的目录。
              -e<有效期限>           #修改帐号的有效期限。
              -f<缓冲天数>           #修改在密码过期后多少天即关闭该帐号。
              -L                    #锁定用户密码，使密码无效。
              -s<shell>             #修改用户登入后所使用的shell。
              -u<uid>               #修改用户ID。
              -U                    #解除密码锁定。
   
   4. groupadd 新建组 命令 groupadd 组名

   5. groupadd 删除组命令  groupdel 组名

   3. .groupmod 更改组命令
              -g, --gid GID                 #将组 ID 改为 GID
              -h, --help                    #显示此帮助信息并推出
              -n, --new-name NEW_GROUP      #改名为 NEW_GROUP
              -o, --non-unique              #允许使用重复的 GID
              -p, --password PASSWORD       #将密码更改为(加密过的) PASSWORD
              -R, --root CHROOT_DIR         #chroot 到的目录
              -P, --prefix PREFIX_DIR       #prefix directory where are located the /etc/* files
             
   7. passwd修改密码命令  passwd修改当前使用用户命令 passwd 用户名修改指定用户密码

### 3.网卡配置

         1.Centos网卡配置文件/etc/sysconfig/network-scripts/ifcfg-[网卡名]
    
         ​	
    
         ```
         DEVICE="eth0"                                #网卡名
         HWADDR="00:0C:29:FD:FF:2A"                   #mac地址
         NM_CONTROLLED="yes"                          #network mamager的参数，实时生效，不需要重启
         ONBOOT="yes"                                 #开机自动链接
         IPADDR=192.168.1.31                          #ip地址
         NETMASK=255.255.255.0                        #子网掩码
         GATEWAY=192.168.1.1                          #网关
         BOOTPROTO=static                             #静态ip
         ```
    
         ​       设置网卡状态命令：
    
         ```
         systemctl restart/stop/start/status network         #重启/停止/启动/查看状态
         service network restart/stop/start/status           #重启/停止/启动/查看状态
         ```
    
         systemctl 和 service 命令
    
         ```
         systemctl restart/stop/start/status network          #重启/停止/启动/查看状态
         service network restart/stop/start/status            #重启/停止/启动/查看状态
         ```
    
         nmcli 命令
    
         ```
         nmcli c show                 #查看状态
         nmcli c up/down [网卡名]      #启动/关闭
         nmcli c reload [网卡名]       #重启
         nmcli c modify enp0s3 ipv4.addresses 192.168.0.62/24
         nmcli c modify enp0s3 ipv4.gateway 192.168.0.1
         nmcli c modify enp0s3 ipv4.dns 8.8.8.8
         ```
    
         2.ubuntu网卡配置
    
         Ubuntu上的网卡配置文件存储于`/etc/network/interfaces`
    
         其中主要参数：
    
         ```
         # 若设置静态IP
         auto [网卡名]            
         iface [网卡名] inet static                            
         address 192.168.3.90                #ip地址
         gateway 192.168.3.1                 #网关
         netmask 255.255.255.0               #子网掩码
         
         # 若设置动态IP
         auto [网卡名]
         iface eth0 inet dhcp
         ```
    
         设置网卡状态
    
         ```
         /etc/init.d/networking restart  #重启网卡
         /etc/init.d/networking start    #启动网卡
         /etc/init.d/networking stop     #关闭网卡
         ```
### 4.DNS配置

         Centos的DNS配置文件存储于`/etc/resolv.conf`  nameserver 114.114.114.114
    
         ubuntu DNS配置文件存储于`/etc/resolv.conf`

### 5.Linux运行级别

         所谓运行级别，简单点来说，运行级别就是操作系统当前正在运行的功能级别。级别是从0到6，具有不同的功能。
    
         Linux下的7个运行级别：
    
         - 0:系统停机状态，系统默认运行级别不能设置为0，否则不能正常启动，机器关闭。
    
         - 1:单用户工作状态，root权限，用于系统维护，禁止远程登陆，就像Windows下的安全模式登录。
    
         - 2:多用户状态，没有NFS支持。
    
         - 3:完整的多用户模式，有NFS，登陆后进入控制台命令行模式。
    
         - 4:系统未使用。
    
         - 5:X11控制台，登陆后进入图形GUI模式，XWindow系统。
    
         - 6:系统正常关闭并重启，默认运行级别不能设为6，否则不能正常启动。运行init6机器就会重启。
    
           关机/重启命令：
    
           ```
           init 0
           init 6
           ```

### 6.sudo配置文件

    
    
    `sudo`配置文件存在于`/etc/sudoers`，root用户如想赋予普通用户sudo权限，则需要修改大概位于98行的内容：
    
           ```
           97 ## Allow root to run any commands anywhere
           98 root    ALL=(ALL)       ALL
           99 
           100 ## Allows members of the 'sys' group to run networking, software,
           101 ## service management apps and more.
           102 # %sys ALL = NETWORKING, SOFTWARE, SERVICES, STORAGE, DELEGATING, PROCESSES, LOCATE, DRIVERS
           103 
           104 ## Allows people in group wheel to run all commands
           105 %wheel        ALL=(ALL)       ALL
           106 
           107 ## Same thing without a password
           108 # %wheel        ALL=(ALL)       NOPASSWD: ALL
           ```
    
           如果给farmsec1用户添加sudo权限：
    
           ```
           farm1    ALL=(ALL)    ALL` 如果给farmsec用户组添加sudo权限： `%farmsec    ALL=(ALL)    ALL
           ```

### 7.定时计划任务文件

`crontab`被用来提交和管理用户的需要周期性执行的任务，与windows下的计划任务类似，当安装完成操作系统后，默认会安装此服务工具，并且会自动启动crond进程，crond进程每分钟会定期检查是否有要执行的任务，如果有要执行的任务，则自动执行该任务

crontab -e` 编辑当前用户的cron表 `crontab -l 查看当前用户的cron表

`crontab -r` 删除当前用户的cron表

`/var/spool/cron/` 目录下存放的是每个用户包括root的crontab任务，每个任务以创建者的名字命名

`/etc/crontab` 这个文件负责调度各种管理和维护任务

`/etc/cron.d/` 这个目录用来存放任何要执行的crontab文件或脚本

我们还可以把脚本放在`/etc/cron.hourly`、`/etc/cron.daily`、`/etc/cron.weekly`、`/etc/cron.monthly`目录中，让它每小时/天/星期、月执行一次

编辑cron的格式为：`分 时 日 月 周 命令`

```
*    #代表任意时间
,    #代表不联系的时间点，2,3 表示2和3都行
-    #代表连续的时间段，比如2-4表示2,3,4
*/n  #代表每隔单位时间
```

案例1：每隔一分钟往1文本里输入一条hello 的信息

```
*/1 * * * * echo "hello">> /root/1
```

案例2：每小时的第一分钟执行一次

```
1 * * * * echo "hello">> /root/1
```

案例3：每小时的第一分钟跟第二分钟执行

```
1,2 * * * * echo "hello">> /root/1
```

其他案例：

```
实例1：每1分钟执行一次myCommand
* * * * * myCommand

实例2：每小时的第3和第15分钟执行
3,15 * * * * myCommand

实例3：在上午8点到11点的第3和第15分钟执行
3,15 8-11 * * * myCommand

实例4：每隔两天的上午8点到11点的第3和第15分钟执行
3,15 8-11 */2  *  * myCommand

实例5：每周一上午8点到11点的第3和第15分钟执行
3,15 8-11 * * 1 myCommand

实例6：每晚的21:30重启smb
30 21 * * * /etc/init.d/smb restart

实例7：每月1、10、22日的4 : 45重启smb
45 4 1,10,22 * * /etc/init.d/smb restart

实例8：每周六、周日的1 : 10重启smb
10 1 * * 6,0 /etc/init.d/smb restart

实例9：每天18 : 00至23 : 00之间每隔30分钟重启smb
0,30 18-23 * * * /etc/init.d/smb restart

实例10：每星期六的晚上11 : 00 pm重启smb
0 23 * * 6 /etc/init.d/smb restart

实例11：每一小时重启smb
0 */1 * * * /etc/init.d/smb restart

实例12：晚上11点到早上7点之间，每隔一小时重启smb
0 23-7/1 * * * /etc/init.d/smb restart
```

我们使用`crontab -l`的命令是无法查看到其他用户的计划任务的。farm2用户的计划任务保存在`/var/spool/cron/`目录下。

可以使用如下命令进行查看：

```
crontab -u <user> -l
```

### 8.开机自启文件

`/etc/rc.d/rc.local` 用于添加开机启动命令,`/etc/rc.local`是`/etc/rc.d/rc.local`的软链接，软连接相当于windows的快捷方式。

系统开机后会自动执行`/etc/rc.local`,换句话说，只要将你想执行的命令写入这个文件，`vi /etc/rc.local`，就可以做到开机自动执行。

如我们在此处写入`echo 'hello' >> /root/hello`。

如果你是第一次使用这个文件，需要对其加上执行权限。`chmod +x /etc/rc.local`