# 将命令的返回值复制给变量

```
A=`ls -la` 反引号，运行里面的命令，并把返回的结果赋值给变量A

A=$(ls -la)  等价于反引号
```

### Shell 中的特殊变量

* $?  表示上一个命令退出的状态
	
	0 成功执行
	
	非0 ，执行失败

```
[#Mac:Desktop]$ lss
-bash: lss: command not found
[#Mac:Desktop]$ echo $?
127
[#Mac:Desktop]$

[#Mac:Desktop]$ ls
16841528774453_.jpg
855959-20170315213605791-1699232226.png
Linux...

[#Mac:Desktop]$ echo $?
0
```

$$ 表示当前进程编号

$0 表示当前脚本名称

$n 表示位置变量 （n代表数字，n>=1）

```
vi test.sh

#!/bin/bash

echo "第一个参数 = "$1
echo "第二个参数 = "$2
```
执行

```
[#Mac:tmp]$ chmod a+x test.sh
[#Mac: tmp]$ sh test.sh hello china
第一个参数 = hello
第二个参数 = china
[#Mac: tmp]$

```

$# 表示变量个数，常用于循环

$* 和$@都表示参数列表

### $* 和 $@ 的区别

$*和$@都是传递给函数或者脚本的所有参数，不被双引号“”包含时，都以$1,$2 ... $n 的形式输出所有参数

当它们被双引号“” 包含时，“$*”会将所有的参数作为一个整体，以“$1$2..$n”的形式输出所有参数；“$@”会将各个参数分开,以“$1“ ”$2“..”$n”的形式返回所有参数

### for 循环

* 第一种
 for N in 1 2 3
 do
 	echo $N
 done
 
 
 或者
 for N in 1 2 3; do echo $N;done
 
 或者
 
 for N in {1..3};do echo $N;done


### 运算符

格式： expr m + n 或者$((m+n)) 注意expr运算符间要有空格

例如计算：（2 + 3）x 4 的值

```
1 分步计算
	S=`expr 2 + 3`
	expr $S\* 4
2 一步完成计算
	expr `expr 2 + 3`\* 4
	ehco `expr \`expr 2 + 3 \`\* 4
	或者
	$(((2+3)*4))

```

#### read 命令

* read -p(提示语) -n(字符个数) -t(等待时间)

read -p "please input your name:"name
echo $name

<!--

create time: 2018-06-27 11:10:53
Author: Alfred

This file is created by Marboo<http://marboo.io> template file $MARBOO_HOME/.media/starts/default.md
本文件由 Marboo<http://marboo.io> 模板文件 $MARBOO_HOME/.media/starts/default.md 创建
-->

