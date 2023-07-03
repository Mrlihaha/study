#### 1.NFS介绍

NFS（Network File System）即网络文件系统，是FreeBSD支持的文件系统中的一种，它允许网络中的计算机之间通过TCP/IP网络共享资源。在NFS的应用中，本地NFS的客户端应用可以透明地读写位于远端NFS服务器上的文件，就像访问本地文件一样

#### 2.NFS服务搭建

在NFS的服务中，主要依赖于nfs-utils与rpcbind的来进行服务

```
dnf -y install rpcbind nfs-utils
```

rpcbind的作用是管理RPC的绑定机制，帮助客户端和服务端之间建立连接和通信。它提供了动态端口分配、服务发现和端口监听管理等功能，使得RPC程序能够在网络中正常运行。

<span style="color:red">所以在NFS的服务的启动中，启动rpcbind后再启动nfs服务</span>

```
systemctl start rpcbind
systemctl start nfs-server
systemctl enable rpcbind
systemctl enable nfs-server
```

启动服务后查看rpc服务的注册情况，可使用rpcinfo的指令进行查看。

```
rpcinfo -p localhost
```

#### 3.NFS的配置文件

NFS的配置文件为/etc/exportfs

配置文件每行分为两段：第一段为共享的目录，使用绝对路径，第二段为客户端地址及权限

```
地址可以使用完整IP或网段，例如10.0.0.8或10.0.0.0/24，10.0.0.0/255.255.255.0当然也可以地址可以使用主机名，DNS解析的和本地/etc/hosts解析的都行，支持通配符，例如：*.fsec.io  
 权限有：  
·  rw：read-write，可读写； 
·  ro：read-only，只读；  
·  sync：文件同时写入硬盘和内存；  
·  async：文件暂存于内存，而不是直接写入内存；  
·  no_root_squash：NFS客户端连接服务端时如果使用的是root的话，那么对服务端分享的目录来说，也拥有root权限。  
·  root_squash：NFS客户端连接服务端时如果使用的是root的话，那么对服务端分享的目录来说，拥有匿名用户权限，通常他将使用nobody或nfsnobody身份；  
·  all_squash：不论NFS客户端连接服务端时使用什么用户，对服务端分享的目录来说都是拥有匿名用户权限；  
·  anonuid：匿名用户的UID值，通常是nobody或nfsnobody，可以在此处自行设定；  
·  anongid：匿名用户的GID值。
```

<p style="color:red">exports目录权限中，有这么一个参数no_root_squash </p>

 NFS 主机分享目录的使用者，如果是 root 的话，那么对于这个分享的目录来说，他就具有root 的权限！。默认情况使用的是相反参数root_squash：在登入 NFS 主机使用分享之目录的使用者如果是 root 时，那么这个使用者的权限将被压缩成为匿名使用者，通常他的UID 与 GID 都会变成 nobody 那个身份。

在NFS服务器上创建一个目录用于共享文件 mkdir /share1  mkdir /share2 编辑/etc/exportfs文件输人入

```
/share1 *(sync,ro) 192.168.0.50(sync,rw)
/share2 192.168.0.0/24(sync,ro)
```

编辑完成后，需要运行exportfs重新发布所共享的目录。exportfs -r 重新读取配置文件 

服务器用exportfs查看本地共享文件夹

客户端使用<span style="color:red">showmount -e ip  查看指定NFS服务器共享目录</span>

####4. mount命令

mount命令用于挂载磁盘,在NFS服务中需要使用mount -t nfs 或 mount,nfs的指令进行指定挂载

使用挂载钱在客户端创建挂载目录 随后使用mount命令江远程主机的目录挂载至本机的目录

mkdir /mnt/share1 /mnt/share2

mount -t nfs 10.10.50.231:/share1 /mnt/share1

mount.nfs 10.10.50.231:/share2 /mnt/share2 挂载后进入挂载目录ls查看是否挂载成功

#### 5.客户端自动挂载与备份

客户端与NFS服务器有一个重启或关机挂载的目录就会自动断开

1.通过如下的命令，我们重新调整NFS的配置。

```
mkdir /backup
vi /etc/exports
cat /etc/exports
/backup 192.168.0.0/24(sync,rw,no_root_squash)
exportfs -r
exportfs
```

然后使用开机自启文件rc.local 进行自动挂载,在/etc/rc.local文件中添加如下内容：

```
mount.nfs 192.168.0.33:/backup /mnt/backup/
```

随后创建挂载点，并给/etc/rc.local执行权限。

```
mkdir /mnt/backup
chmod +x /etc/rc.local
```

重启主机后，可进入/mnt/backup目录中查看是否存在测试的文件即可。

2.通过计划任务进行自动挂载与备份

首先先编写一个脚本

```
#!bash
mount.nfs 192.168.0.33:/backup /mnt/backup
date="$(date +%F)"
cp /etc/passwd /mnt/backup #然后在在开机计划中执行脚本 
```

 
