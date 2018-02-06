处理原则：

1、sed一次只处理一行内容

2、sed不改变文件内容( 除非重定向 )

基本格式：

一、sed文本处理过程

1. 正则表达式选定文本调用sed操作命令进行处理

二、sed的格式

1. 命令行格式

1.1 sed [options] ‘command’ file(s)


1.1.1 options：-e ; -n

1.1.2 command：行定位（正则）+sed命令（操作）

1.1.3 例如：sed -n '/root/p'

1.1.4 例如：sed -e '10,20d' -e 's/false/true/g'

2. 脚本格式

2.1 sed -f scriptfile file(s)

一、基本操作命令

1. p命令：打印相关的行

1.1 sed 'p' passwd #会打印出两行,因为sed的原理是读入一行，输出一行，此
处再加上p命令打印出来的一行，所以最后会打印出两行

1.2 sed -n 'p'passwd #加了-n选项之后，只会打印出相关的行，那些不相关的行则不会打印出来

二、行定位

1. 定位一行：x；（x为数字，行号的意思），/pattern/（pattern为正则表达式，正则两边加上//） sed命令第一个//内的为正则表达式，其他的//内的为字符串

例如：sed 's/^[a-Z]\+/root/' password 前一个//内的是正则表达式，后一个//内的是字符串

1.1 例如：sed -n '10p' passwd #通过行号定位，定位到第10行，并打印到屏幕但是无法看到行号

1.2 例如：nl passwd | sed -n '10p' #使用nl命令显示文件的行号，再通过管道传递给sed进行行处理。

1.3 例如：sed -n '/mooc/p' passwd #通过正则定位，定位含有mooc字符串这一行，并打印到屏幕

2. 定位几行：

2.1 x,y #（定位一个区间，x行到y行）

2.1.1 例如：nl passwd | sed -n '10,20p'

2.2 x,/pattern/ #（定位一个区间，x为开始的行号，pattren为结束的正则所
表示的行）

2.2.1 例如：nl passwd | sed -n '10,/mooc/p'

2.3 /pattern1/,/pattern2/ #（定位一个区间，pattern1为开始的正则1所表
示的行，pattern2为结束的正则2所表示的行）

2.3.1 例如：nl passwd | sed -n '/news/,/mooc/p'

2.4 “!” #感叹号，对所选的行进行取反

2.4.1 例如：nl passwd | sed -n '10!p'

2.4.2 例如：nl passwd | sed -n '10,20!p'

3.定位间隔几行：

3.1 first~step （first为开始的行，setp为步长也就是间隔几行）

3.1.1 例如：nl passwd | sed -n '1~2p' #定位从第1行开始，中间间隔2行
的所有的行

以下是对整行进行操作

一、a 命令 #新增加一行，在所选行的下面新增一行，并输入内容

1. 命令格式：sed ‘行号+a命令+需要增加的内容’
2. 例如：nl passwd | sed '5a======' #在第5行的下面新增一行，并写入
3. ======这些内容
3. 例如：nl passwd | sed '1,5a======' #在第1-5行每一行下面都增加一行并写入同样的内容

二、i 命令 #新插入一行，在所选行的上面插入一行，并输入内容

1. 命令格式：sed ‘行号+i命令+需要插入的内容’
2. 例如：nl passwd | sed '5i======' #在第5行的上面插入一行，并写入======这些内容
3. 例如：nl passwd | sed '1,5i======'#在第1-5行每一行上面都插入一行并写入同样的内容

三、c 命令 #替换一行，将所选定的行的内容替换成需要替换的内容

1. 命令格式：sed ‘行号+c命令+需要替换的内容’
2. 例如：nl passwd | sed '5c======' #将第5行的内容替换成======这些内容
3. 例如：nl passwd | sed '1,5c======' #将1-5行整体的内容都替换成======这些内容

四、d 命令 #删除一行，将所选行的内容全部删除

1. 命令格式：sed ‘行号+d命令’
2. 例如：nl passwd | sed '5d' #将第5行的内容全部删除

六、案例二：文本处理

1. 目标：删除文本中的空行
2. 操作命令：sed '/^$/d' all.txt #删除文本中的空行

以下是对行内的元素进行操作

sed替换命令。

1.如果只是sed ‘s/旧字符/新字符’，那么就只会替换每行第一个遇到的旧字符

2. 因此要在最后加上g，表示全局，才会全部替换
案例获取ipconfig命令中的ip地址
ifconfig | sed -n '2p' | sed 's/inet.*r:/ /' | sed 's/B.*//'

sed命令高级操作：

1、{}，多个sed命令，使用;号分割                            

例如：nl passwd | sed '{5,10d;s/nologin/login/g}' #显示行号，删除
password文件的5到10行，并将该文件的nologin字段全部替换为login

2、 sed -n ‘起始行~行距（每次跳几行）’ 文件名
      sed 中的n命令表示调到下一行，
sed -n '{n;p}' 就可以表示先跳到下一行，然后再开始打印，结果就是输出所有的偶数行

例子：nl passwd | sed -n '{n;p}' #显示行号，输出password文件的偶数行

例子：nl passwd | sed -n '{p;n}' #显示行号，输出password文件的奇数行

sed -n ‘起始行~行距（每次跳几行）’ 文件名

例子： nl passwd | sed -n '1~2p' #显示行号，输出password文件的偶数行

例子： nl passwd | sed -n '2~2p' #显示行号，输出password文件的奇数行

3、&符号表示的是正则表达式匹配到的的内容

例如1：将password文件用户名后面拼接上空字符串，使其与后面的字符有空格间隔
sed 's/^[a-Z]\+/& /' passwd

例如2：将password文件用户名进行大小写转换
\u \l \U \L转换大小写，前边小写u和l仅变更开头字符，大写U和L变更全部

sed 's/^[a-z-_]\+/\u&/' /etc/passwd 将用户名首字母转大写

sed 's/^[a-z-_]\+/\u&/' /etc/passwd将用户名第一个单词转大写

sed 's/^[a-z-_]\+/\l&/' /etc/passwd 将用户名首字母转小写

sed 's/^[a-z-_]\+/\L&/' /etc/passwd 将用户名第一个单词转小写

例3：将文件夹下的所有文件名，进行大小写转换

```

[root@iZ2ze1yhgn9t43ir5x8qr8Z test]# ls | sed 's/^\w\+/\U&/'
A.txt
B.txt
C.txt
D.txt

```



4、( )号对匹配到的内容进行分组
例子1：将password文件中的用户名提取出来

	[root@iZ2ze1yhgn9t43ir5x8qr8Z ~]# sed 's/^\(\w\+\):.*$/\1/' passwd
	root
	phpwww


例子2：获取password文件中的用户名、uid、和gid

	[root@iZ2ze1yhgn9t43ir5x8qr8Z ~]# sed 's/^\(\w\+\):x:\([0-9]\+\):\([0-9]\+\).*$/USER:\1 UID:\2 GID\3/' passwd 
	USER:root UID:0 GID0
	USER:bin UID:1 GID1


5、( )符号，替换某种（或某部分字符串）

	[root@iZ2ze1yhgn9t43ir5x8qr8Z ~]# ifconfig  | sed -n '2p' | sed 's/^\([a-z ]\+:\)\([0-9\.]\+\)\(.*\)$/\2/'
	172.17.167.8
	
6、-r：复制指定文件插入到匹配行；

-w:复制匹配行已重写文件的方式，写入到目标文件里，即目标文件的内容，将被替
换成你匹配到的内容
  源文件————————————-》目标文件
  源文件《 ————————————目标文件

例子：
初始化文件 a.txt 和 1.txt

	[root@iZ2ze1yhgn9t43ir5x8qr8Z test]# cat a.txt 
	aaaaaaaaaaaaaaaaaaaaaaa
	bbbbbbbbbbbbbbbbbbb
	ccccccccccccccccccccc
	[root@iZ2ze1yhgn9t43ir5x8qr8Z test]# cat 1.txt 
	11111111111111111111111111
	222222222222222222222
	333333333333333

例子1：sed ‘2r 1.txt’ a.txt #将1.txt文件的所有内容，保持原格式，插入到a.txt文件的第2行，结果如下（实际上并没有对a.txt文件修改）

	[root@iZ2ze1yhgn9t43ir5x8qr8Z test]# sed '2r 1.txt' a.txt 
	aaaaaaaaaaaaaaaaaaaaaaa
	bbbbbbbbbbbbbbbbbbb
	11111111111111111111111111
	222222222222222222222
	333333333333333
	ccccccccccccccccccccc

例子1：sed ‘2w 1.txt’ a.txt #将a.txt文件的第2行，写入1.txt文件，可以理解为，是拿a.txt文件的第2行内容，替换1.txt文件的全部内容，
结果如下（实际上并没有对a.txt文件修改，但是1.txt文件已经被修改了）

	[root@iZ2ze1yhgn9t43ir5x8qr8Z test]# sed '2w 1.txt' a.txt 
	aaaaaaaaaaaaaaaaaaaaaaa
	bbbbbbbbbbbbbbbbbbb
	ccccccccccccccccccccc
	[root@iZ2ze1yhgn9t43ir5x8qr8Z test]# cat 1.txt 
	bbbbbbbbbbbbbbbbbbb
	[root@iZ2ze1yhgn9t43ir5x8qr8Z test]# cat a.txt 
	aaaaaaaaaaaaaaaaaaaaaaa
	bbbbbbbbbbbbbbbbbbb
	ccccccccccccccccccccc

7、-q 退出sed命令

例子1. nl /etc/passwd | sed '10q' /etc/passwd

\#代表只显示password文件前十行然后就提前结束

例子2. 
  nl /etc/passwd | sed '/nologin/q' /etc/passwd
  
\# 代表匹配到password文件中的第一个nologin字符串，就退出sed


[来自网络](http://blog.csdn.net/zziazero/article/details/73543193#t0)