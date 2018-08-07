# sed 介绍

###sed替换命令(重点)1 替换命令格式sed '[address-range|pattern-range] s/originalstring/replacement-string/[substitute-flags]'inputfileaddress-range:地址列表，表示从哪个地方开始执行，如:1，3 表示第一行 到第3行。 

pattern-range:样式列表，表示从哪个样式匹配串开始。如:/Jane/,表示 从行中包含字符串 Jane 的行开始执行。[]中的都是可选的。 s:表示要执行替换命令(substitute) 

originalstring:需要被替换的字符串 replacement-string:替换后的字符串。 

substitute-flags:可选替换标志符。###2 准备工作新建一个 example.txt 文本，输入以下内容: 

```
101,John Doe,CEO102,Jason Smith,IT Manager103,Raj Reddy,Sysadmin104,Anand Ram,Developer
105,Jane Miller,Sales Manager```3 address-range 和 pattern-range
a) 把第一行的 John 替换成 guozi 字符串 

	sed '1 s/John/guozi/' example.txtb) 把第二行至第五行的 Manager 替换成 guozi 

	sed '2,5 s/Manager/guozi/' example.txtc) 把 John 所以字符串行当中的 CEO 替换成 XXX 

	sed '/John/ s/CEO/XXX/' example.txtd) 从第四行而且包含字符串 Ram 的行中，把 Developer 字符串替换成 XXX 

	sed '4,/Ram/ s/Developer/XXX/' example.txte) 如果没有 address-range 和 pattern-range，那么就会把每一行碰到的第一 个匹配字符串给替换掉。	
	sed 's/1/AAA/' example.txt###4 substitute-flags
3.1 全局标志 g(globe) 全局标志会把遇到的所有满足条件的字符串给替换掉
	sed 's/1/AAA/g' example.txt3.2 数字标志 1,2,3把要匹配串中的第 2 个符合条件的匹配串替换成我们想要的字符串	sed 's/1/AAA/2’ example.txt3.3 又见 p(print)标志	sed -n 's/1/AAA/p' example.txt3.4 写标志 w(write)把每一行碰到第一个字符串 John 替换成 guozi 字符串，并写入output.txt 中	
	sed 's/John/guozi/w output.txt' example.txt对比重定向重定向会把所以输出到终端的内容全部放到文本当中，而 w 开关只会把 我们所做过修改的行写入文件当中。3.5 敏感标志 i(ignore flag)忽略大小写，把文本当中所有包含 jason 字符串的行替换成 AAAA 字符串    sed 's/jason/AAAA/gi' example.txt3.6 联合使用标志 可以使用多个标志来对字符串进行替换并保存       sed 's/jason/AAAA/gipw output2.txt' example.txt5 替换命令定界符格式 1:Sed ‘s///’ example.txt

格式 2:Sed ‘s@@@’ example.txt 用处:使用合理的定界符可以方便的阅读我们的程序代码。6 强大的&——样式匹配将 origin-string 替换到 replace-string 当中 

	sed 's/John/[&]/' example.txt 

需求:将开头为三位数字的外面再加一层{} 
	
	sed 's/^[0-9]\{3\}/{&}/' example.txt作业:将如下的文本前面三位数截取出来，并加上大括号```101,John Doe,CEO102,Jason Smith,IT Manager 103,Raj Reddy,Sysadmin 104,Anand 
Ram,Developer 105,Jane Miller,Sales Manager 
```
最终输入效果为:```{101}{102}{103}{104}{105}```


<!--
create time: 2018-06-25 07:51:53
Author: Alfred

This file is created by Marboo<http://marboo.io> template file $MARBOO_HOME/.media/starts/default.md
本文件由 Marboo<http://marboo.io> 模板文件 $MARBOO_HOME/.media/starts/default.md 创建
-->

