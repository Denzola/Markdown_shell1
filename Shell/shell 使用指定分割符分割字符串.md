# shell 使用指定分割符分割字符串


```
#!/bin/bash
string="hello,shell,haha"  
array=(${string//,/ })  
for var in ${array[@]}
do
   echo $var
done 

```


###方法2

```
#!/bin/bash
string="hello,shell,haha"
OLD_IFS="$IFS"
IFS=","
array=($string)
IFS="$OLD_IFS"
for var in ${array[@]}
do
   echo $var
done

```

<!--
create time: 2018-05-23 17:27:14
Author: Alfred

This file is created by Marboo<http://marboo.io> template file $MARBOO_HOME/.media/starts/default.md
本文件由 Marboo<http://marboo.io> 模板文件 $MARBOO_HOME/.media/starts/default.md 创建
-->

