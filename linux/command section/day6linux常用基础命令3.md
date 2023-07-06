### 1.awk命令

`awk`处理过程: 依次对每一行进行处理，然后输出

awk '{print}' fliename 输出文件的内容

awk '{print $1}' filename输出文件每行文件每行第一个空格前的内容,没空格则输出整行

awk -F ":"  '{print $1}' filename 输入文件每行第一个:号前的内容

awk -F ":"  '{print "文字1"$1"文字2"$3} filename 输入文件每行第1-3个:号前的内容并附加文字

awk -F ":" '{print $NF}'   filename  打印出文本中每行的最后一个字段

awk -F ":" '{print $(NF-1)}'   filename  打印出文本中每行的倒数第二个字段

### 2. sed命令

```
1. s：替换命令，以 / 分隔，用第二个 / 后面的内容替换第一个 / 前面的内容，例如：`sed 's/old/new/' file.txt` 表示将 `file.txt` 中前一次出现的 `old` 替换为 `new`
2. d：删除命令，例如：`sed '/pattern/d' file.txt` 表示删除 `file.txt` 中包含 `pattern` 的行
3. a：插入命令，在指定的行后面插入文本，例如：`sed '3a\ This is a new line' file.txt` 表示在第三行后面插入 `This is a new line`
4. i：插入命令，在指定的行前面插入文本，例如：`sed '3i\ This is a new line' file.txt` 表示在第三行前面插入 `This is a new line`
5. p：打印命令，将匹配模式的行打印出来，例如：`sed -n '/pattern/p' file.txt` 表示只打印 `file.txt` 中包含 `pattern` 的行
```

示例:

\#为定界符需要转义的字符在定界符内则不需要转义

sed 's/e/w/g' /etc/passwd 打印出 /etc/passwd文本中的e全部换成w后的内容    3g则从第三个开始换

命令 sed -e  's#^#https://#' -e 's#$#/login.php#g' test.txt  或  cat test.txt | sed -e  's#^#https://#' -e 's#$#/login.php#g' 

test.txt文档内容为 www.baidu.com 执行上面命令输入为 https://www.baidu.com/login.php命令只查看文本内容不修改 如要修改在sed后面加 -i

sed ' /^$/d' 删除文件内所有空白行

命令 sed ' /^s/d' 删除文件所有开头是s的行

### 3.grep命令

grep用于筛选文本内容示例如下:

grep root filename 筛选文本里面含有root关键字的行

grep root filename1 filename 在多个文本中查找

grep -E "root|user" filename 筛选文本内容含有root或user的行

grep -v -e root -e user filename 或 grep -v -E "root|user"筛选文本中不含有root或user关键字的所有行

grep -E "^root" filename 查找文本中以root开头的行

grep -v -E filename 查找文本中不以root开头的行

grep -v root filename 筛选文本中不含有root关键字的所有行

grep "passwd" /etc -rn    -r 为递归 -n 显示关键字出现在第几行   在多级目录中对文本进行递归查找

grep root test*   从以test开头的所有文件查找是否有带root关键字的行

### 4. find命令

`find` 命令用来在指定目录下查找文件。任何位于参数之前的字符串都将被视为欲查找的目录名。如果使用该命令时，不设置任何参数，则 find  命令将在当前目录下查找子目录与文件。并且将查找到的子目录和文件全部进行显示。

示例: 
find / -name "*hello*" 查找含有hello关键字的文件
find / -size 1000k 从 根目录下查找大小为1000k的文件 +1000k表示大于1000k				

find / -name '1*' 从根目录下查找以以1开头的文件

find / -name '*1' 从根目录下查找以1结尾的文件

find / -user root 从根目录下查找属主为root的文件

find / -group root 从根目录下查找属组为root的文件 

find / -perm 777 从根目录下1差债权限为777的文件

find /etc  -type d 查找 /etc下所有的目录文件

-type可以指定文件类型参数为

- `f`：普通文件（regular file）。

- `d`：目录（directory）。

- `l`：符号链接（symbolic link）。

- `b`：块设备文件（block special file）。

- `c`：字符设备文件（character special file）。

- `p`：命名管道（FIFO）。

- `s`：套接字（socket）。

  m被修改过 a 被访问过 c 状态和权限被修改过 time 天 min分钟

find  / -type -f -mtime -7 搜索7天内根目录下修改过的文件（-7代表7天内，7代表七天前那一天，+7代表7天前

find / -type f -mmin -10 搜索根目录下10分钟内修改过的文件

find / -type f -atime 搜索根目录下一天内被访问的文件  -1代表1天内，1代表前1天那一天，+1代表1天前

find / -type f -ctime -1 搜索根目录下一天内状态被改变（列如权限）的文件（-1代表1天内，1代表前1天那一天，+1代表1天前

find / -type f -cmin -10 搜索根目录下10分钟之内状态被改变的文件

### 5.sort命令

sort命令是用于给文件内容进行排序示例:

cat /etc/passwd | sort  默认按照首个字符排序先排数字 再排字母

cat /etc/passwd | sort -n 用于按照字母排序

### 6.uniq命令

uniq用于列出相同的行和列出重复的次数一般与sort结合使用示例:

cat 

### 7.stat命令

用法 stat 文件或目录例如

stat /etc/passwd 会显示
  文件：/etc/passwd
  大小：3164      	块：8          IO 块大小：4096   普通文件
设备：8,2	Inode: 19663901    硬链接：1
权限：(0644/-rw-r--r--)  Uid: (    0/    root)   Gid: (    0/    root)
访问时间：2023-06-21 17:35:01.022276803 +0800
修改时间：2023-06-20 17:34:52.969988451 +0800
变更时间：2023-06-20 17:34:52.977988590 +0800
创建时间：2023-06-20 17:34:52.965988382 +0800

### 8. xargs命令

xargs 用于分行把多少和字符分成一行

例如 1 2 4 5 6 7  xargs -n 2就会分为124和567两行 

### 9.nl

nl 用于列出文件内容的行号 用法nl filrname

### 10.ssh连接

用法; ssh -l 用户名 ip地址 也可写成 ssh 用户名@ip地址

### 11. curl weget

`curl`命令是一个利用URL规则在命令行下工作的文件传输工具。它支持文件的上传和下载，所以是综合传输工具，但按传统，习惯称curl为下载工具。作为一款强力工具，curl支持包括HTTP、HTTPS、ftp等众多协议，还支持POST、cookies、认证、从指定偏移处下载部分文件、用户代理字符串、限速、文件大小、进度条等特征。

用法：`curl [选项] [url]`

参数：

- -i  显示头部信息
- -v 显示请求全过程解析
- -O 下载



wget命令：用来从指定的URL下载文件。wget非常稳定，它在带宽很窄的情况下和不稳定网络中有很强的适应性，如果是由于网络的原因下载失败，wget会不断的尝试，直到整个文件下载完毕。如果是服务器打断下载过程，它会再次联到服务器上从停止的地方继续下载。这对从那些限定了链接时间的服务器上下载大文件非常有用。

```bash
wget https://bootstrap.pypa.io/get-pip.py
```

前面我们下载下的文件名都是乱码，所以我们需要给它自定义文件名 加入-O的参数即可指定文件名

```bash
wget  https://bootstrap.pypa.io/get-pip.py -O 123.py
```

如果文件比较大时，加入参数`-b`，进行后台下载，然后可使用使用`tail -f wget-log`查看进度。

