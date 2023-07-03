#### 1.Debian体系软件安装

   ###### 1.dpkg命令

 用法：

```
dpkg -i     # 安装软件包
dpkg -r     # 移除软件（保留配置）
dpkg -P     # 移除软件（不保留配置）
dpkg -c     # 列出deb包的内容
dpkg -l     # 配合|grep，查找主机包
dpkg -s     # 查找包的详细信息
dpkg -L     # 查看已安装的软件包，都存在系统哪有文件
dpkg -S     # 显示指定包的状态信息
```

###### 2.apt命令

`apt`是一个在Debian中的Shell前端软件包管理器。

`apt`命令提供了查找、安装、升级、删除某一个、一组甚至全部软件包的命令，而且命令简洁而又好记。

`apt`命令执行需要超级管理员权限(root）使用apt有源也可以自定义源kali系统的源文件在`/etc/apt/sources.list`

#### 2.readhat体系软件安装

   ###### 1.rpm命令

用法:

```
rpm -ivh [package_name]         #安装软件包
rpm -evh [package_name]         #卸载软件
rpm -qlp *.rpm                  #列出rpm包的内容
rpm -qa |grep [字符串]           #在已安装的所有软件中查询包含某字符串的软件版本
rpm -ql [软件名]                 #列出该软件所有文件与目录所在的完整文件名
rpm -qc [软件名]                 #列出软件的所有设置文件
rpm -qR [软件名]                 #查询某软件依赖的其他软件
rpm -qf [文件名]                 #查询文件属于哪个软件包
```

###### 2.yum命令

同apt命令一样，`yum`依然从源获取软件。在centos中yum源文件存储在`/etc/yum.repos.d`目录中。

epel源是redhat系比较常用的源。

EPEL (Extra Packages for Enterprise Linux)是基于Fedora的一个项目，为“红帽系”的操作系统提供额外的软件包，适用于RHEL、CentOS和Scientific Linux.

安装epel源只需要安装一个叫”epel-release”的软件包，这个软件包会自动配置yum的软件仓库。

命令为：`dnf install -y epel-release`

yum命令用法如下:

```
yum makecache                         #更新源（安装新源后执行）
yum clean all                         #清除缓存目录（/var/cache/yum）下的软件包及旧的headers
yum list |grep                        #显示所有已经安装和可以安装的程序包
yum info [package-name]               #显示安装包信息
yum -y install [package-name]         #安装软件,默认选yes
yum remove [package-name]             #卸载
yum deplist rpm                       #查看程序rpm依赖情况
yum update                            #更新全部软件包
yum group list                        #列出组
yum group install "Security Tools"    #安装‘Security Tools’软件组
yum search                            #检索安装包
```

###### 3.dnf命令

`DNF`是新一代的rpm软件包管理器。它正在逐步取代`yum`命令

dnf用法如下:

```
dnf repolist                                           #该命令用于显示系统中可用的 DNF 软件库
dnf repolist all                                       #该命令用于显示系统中可用和不可用的所有的 DNF 软件库
dnf list                                               #用户系统上的所有来自软件库的可用软件包和所有已经安装在系统上的软件包
dnf list installed                                     #该命令用于列出所有安装了的 RPM 包
dnf list available                                     #用于列出来自所有可用软件库的可供安装的软件包
dnf search [pakage]                                    #用该命令来搜索软件包
dnf provides /bin/bash                                 #查找某一文件的提供者
dnf info nano                                          #查看软件包详情
dnf install [pakage]                                   #安装软件包
dnf update systemd                                     #该命令用于升级指定软件包
dnf check-update                                       #该命令用于检查系统中所有软件包的更新
dnf update                                             #该命令用于升级系统中所有有可用升级的软件包
dnf remove [pakage]                                    #删除系统中指定的软件包
dnf autoremove                                         #删除无用孤立的软件包
dnf clean all                                          #删除缓存的无用软件包
dnf history                                            #查看您系统上 DNF 命令的执行历史
dnf grouplist                                          #该命令用于列出所有的软件包组
dnf groupinstall ‘Educational Software’                #该命令用于安装一个软件包组
dnf groupupdate ‘Educational Software’                 #升级一个软件包组中的软件包
dnf groupremove ‘Educational Software’                 #该命令用于删除一个软件包组
dnf reinstall [pakage]                                 #该命令用于重新安装特定软件包
```

#### 3.解包压缩包命令

1. 解包常见文件格式的命令：
   - .tar 文件：`tar -xvf 文件名.tar`
   - .tar.gz 或 .tgz 文件：`tar -xzvf 文件名.tar.gz`
   - .tar.bz2 或 .tbz 或 .tbz2 文件：`tar -xjvf 文件名.tar.bz2`
   - .tar.xz 文件：`tar -xJvf 文件名.tar.xz`
   - .zip 文件：`unzip 文件名.zip`
   - .rar 文件：`unrar x 文件名.rar`
   - .7z 文件：`7za x 文件名.7z`
2. 压缩文件的命令：
   - .tar 文件：`tar -cvf 目标文件名.tar 源文件或目录`
   - .tar.gz 文件：`tar -czvf 目标文件名.tar.gz 源文件或目录`
   - .tar.bz2 文件：`tar -cjvf 目标文件名.tar.bz2 源文件或目录`
   - .tar.xz 文件：`tar -cJvf 目标文件名.tar.xz 源文件或目录`
   - .zip 文件：`zip -r 目标文件名.zip 源文件或目录`
   - .rar 文件：`rar a 目标文件名.rar 源文件或目录`
   - .7z 文件：`7za a 目标文件名.7z 源文件或目录`