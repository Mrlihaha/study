#### 1.开启kali的root用户登录

安装完kalI系统后系统禁止了root用户登录开启root登录后有两种方法

第一种方法如下：

所需命令：

```bash
sudo -s
vi /etc/pam.d/gdm-autologin
vi /etc/pam.d/gdm-password
vi /etc/passwd
password
init 6
```

具体操作如下：

1.1使用命令`sudo -s`，并输入密码。

1.2输入命令：`vi /etc/pam.d/gdm-autologin`将第三行文本注释

1.3输入命令`vi /etc/pam.d/gdm-password`，将第三行文本注释：

`:wq`保存退出。

1.4输入命令`vi /etc/passwd`。将文本第一行/usr/bin/zsh改为 /bin/bash。此方法不改也可以使用默认的shell两者没有多大区别

1.5输入`password`命令设置root用户密码。

1.6输入`init 6`重启计算机。（`init 0`为关机）

1.7重启后登入root用户

第二种方法是通过安装软件包实现root登录。

使用`apt-get install kali-root-login`命令安装软件包。(安装前apt update更新软件包列表)

安装完成后，输入命令`passwd`修改root密码。

`init 6`重启后使用root登录。

#### 2.安装NVDIA显卡驱动

部分使用nvidia显卡的计算机在安装kali后会产生缺少驱动不兼容的情况。解决这一问题可参考如下步骤:

以下命令中，执行`apt-cache search linux-image`命令可检索kali系统最新版本内核。系统内核是操作系统最基本的部分。在linux环境中，某些软件和驱动在安装过程中需要调用最新的内核版本。因此我们需要找到最新版本系统内核并安装升级。


> apt-cache search linux-image
>
> apt-get install linux-image-xxx linux-header-xxx
>
> init 6
>
> echo -e "blacklist nouveau\noptions nouveau modeset=0\nalias nouveau off" > /etc/modprobe.d/blacklist-nouveau.conf 禁用kali安装后默认的驱动
>
> update-initramfs -u && reboot
>
> apt-get install dkms
>
>   下载nvidia相应的驱动程序后，用以下命令执行安装。
>
> ./NVIDIA-Linux-x86_64-470.74.run         #全部选是即可
>
>  安装驱动后需要编辑一些配置文件
>
> vi /etc/X11/xorg.conf   把下列内容粘贴到最后面
>
> ```
> Section "ServerLayout"
>  Identifier "layout"
>  Screen 0 "nvidia"
>  Inactive "intel"
> EndSection
> 
> Section "Device"
>  Identifier "nvidia"
>  Driver "nvidia"
>  BusID "PCI:1:0:0"
> EndSection
> 
> Section "Screen"
>  Identifier "nvidia"
>  Device "nvidia"
>  Option "AllowEmptyInitialConfiguration"
> EndSection
> 
> Section "Device"
>  Identifier "intel"
>  Driver "modesetting"
> EndSection
> 
> Section "Screen"
>  Identifier "intel"
>  Device "intel"
> EndSection
> ```
>
> vi /usr/share/gdm/greeter/autostart/optimus.desktop  此文件需要创建
>
> ```
> [Desktop Entry]
> Type=Application
> Name=Optimus
> Exec=sh -c "xrandr --setprovideroutputsource modesetting NVIDIA-0; xrandr --auto"
> NoDisplay=true
> X-GNOME-Autostart-Phase=DisplayServer
> ```
>
> vi /etc/xdg/autostart/optimus.desktop   此文件需要创建
>
> ```
> [Desktop Entry]
> Type=Application
> Name=Optimus
> Exec=sh -c "xrandr --setprovideroutputsource modesetting NVIDIA-0; xrandr --auto"
> NoDisplay=true
> X-GNOME-Autostart-Phase=DisplayServer
> ```
>
> 然后init 6重启

#### 3.安装搜狗输入法

官网的包不兼容准备的包已经放在蓝奏云上了下载下来

使用 dpkg  -i  包  来安装 安装异常时用 apt install -f 来修复依赖修复后重启

#### 4.安装wps

[WPS下载地址](https://linux.wps.cn/)为:https://linux.wps.cn/ 下载后 dpkg -i 安装

WPS切换搜狗输入法。部分系统可能会遇到打开WPS后搜狗输入法切换不出来的情况。需要在三个配置文件中加入环境变量。同时卸载英文输入法解决这个问题。(进入文件按大写的G进入最后一行,使用ctrl+shift+v粘贴)

> ```
> vim /usr/bin/wps 
> export XMODIFIERS="@im=fcitx"
> export QT_IM_MODULE="fcitx"
> vim /usr/bin/et
> export XMODIFIERS="@im=fcitx"
> export QT_IM_MODULE="fcitx"
> vim /usr/bin/wpp
> export XMODIFIERS="@im=fcitx"
> export QT_IM_MODULE="fcitx"
> apt-get remove --purge ibus
> ```

完成后重启计算机，可以解决这个问题。

#### 5.火狐开启插件安装

打开浏览器,在地址栏输入`about:config`

在search那一行输入xpinstall.signatures，将xpinstall.signatures.required那一行双击改为false

#### 6.安装谷歌浏览器

进入官网或蓝奏云下载安装包

官网地址：https://www.google.cn/chrome/index.html

安装缺少依赖使用apt install 修复 安装完成后编辑

vi  /opt/google/chrome/google-chrome

把最后一行加上 --no-sandbox 其中--user-data-dir加不加都可以

exec -a "$0" "$HERE/chrome" "$@" --user-data-dir --no-sandbox

#### 7.typora安装

包在蓝奏云上 dpkg -i 安装安装后编辑

```bash
vi /usr/share/applications/typora.desktop
typora导入功能需要下载pandoc包已经放在蓝奏云
```

```bash
修改 exec=typora --no-sandbox
```

#### 8.flameshot截图工具安装

安装：

```bash
apt-get install flameshot
```

设置快捷键：

点击设置—键盘—键盘快捷键—查看及自定义快捷键-自定义快捷键-加号
名称：随便填
命令：flameshot gui
快捷键：同时摁住ctrl+alt+a
点击添加，完成截图快捷键设置

#### 9.安装文本编辑工具mousepad 及安全工具包

apt install mousepad 安装编辑器

apt install kali-linux-large 集成的安全工具包
