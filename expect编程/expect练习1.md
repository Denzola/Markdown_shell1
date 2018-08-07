# expect练习1

[原文链接](http://blog.51cto.com/cuchadanfan/1668816)


还可以手动输入参数来进行执行脚本

```	
[root@hpf-linuxexpect]# cat 2.expect 
#!/usr/bin/expect
set user [lindex $argv 0]
set host [lindex $argv 1]
set passwd "*********"
set cm [lindex $argv 2]            //需要执行的命令，若命令间有空格则要用双引号""引起来
spawn ssh $user@$host
expect {
"yes/no" { send "yes\r"}
"password:" { send "$passwd\r" }
}
expect "]*"
send "$cm\r"
expect "]*"
send "exit\r"
[root@hpf-linux expect]# ./2.expect root 192.168.1.222 "ls -l /tmp/12.txt"
spawn ssh root@192.168.1.222
root@192.168.1.222's password: 
Last login: Sun Jun 28 18:18:55 2015 from 192.168.1.110
[root@webserver ~]# ls -l /tmp/12.txt
-rw-r--r-- 1 root root 5 6月  28 18:18 /tmp/12.txt

```

3、自动同步脚本内所定义的文件或者目录

下面的例子需要两台机器安装的rsync同步命令，不然无法正常传输。

```	
[root@hpf-linuxexpect]# cat 3.expect 
#!/usr/bin/expect
set passwd "**********"
spawn rsync -av  /tmp/    //将远程机器上指定文件的内容同步到本机/tmp上
expect {
"yes/no" { send "yes\r"}
"password:" { send "$passwd\r" }
}
expect eof
[root@hpf-linux expect]# ./3.expect 
spawn rsync -av root@192.168.1.222:/tmp/12.txt /tmp/
root@192.168.1.222's password: 
receiving incremental file list
12.txt
 

sent 30 bytes  received 84 bytes  76.00 bytes/sec
total size is 5  speedup is 0.04
[root@hpf-linux expect]# ls -l /tmp/12.txt 
-rw-r--r-- 1 root root 5 6月  28 2015 /tmp/12.txt
```
4、指定IP和要同步的文件

```
[root@hpf-linuxexpect]# cat 4.expect 
#!/usr/bin/expect
set passwd "*********"
set host [lindex $argv 0]
set file [lindex $argv 1]
spawn rsync -av $file root@$host:$file
expect {
"yes/no" { send "yes\r"}
"password:" { send "$passwd\r" }
}
expect eof
[root@hpf-linux expect]# echo "longls"> /tmp/12.txt 
[root@hpf-linux expect]# ./4.expect 192.168.1.222 /tmp/12.txt //同步客户端/tmp/12.txt中的内容到指定IP机器上
spawn rsync -av /tmp/12.txt root@192.168.1.222:/tmp/12.txt
root@192.168.1.222's password: 
sending incremental file list
12.txt
 
sent 81 bytes  received 37 bytes  236.00 bytes/sec
total size is 7  speedup is 0.06
[root@hpf-linux expect]# ./2.expect root 192.168.1.222 "cat /tmp/12.txt"     //查看是否同步成功
spawn ssh root@192.168.1.222
root@192.168.1.222's password: 
Last login: Sun Jun 28 18:25:05 2015 from 192.168.1.110
[root@webserver ~]# cat /tmp/12.txt
longls

```
四、构建文件分发系统

实验需求：

        由于公司的代码更新，需要将你管理的几十台机器的相关文件进行同步更新，我们可以用自动化运维工具puppet来实现，也可以自己一个个的用rsync进行同步，也可以用expect结合shell脚本进行同步处理，目前三种方法都有其优缺点，用puppet运维工具很高大上，但需要的运维人员经验较高，自己用rsync虽然很简单但是工作效率很低，最后以我目前的水平就使用expect+shell的脚本结合进行做文件同步处理。


实验环境介绍：

1、每台需要同步机器的密码要相同，若都不相同则还要想办法，不过为了快速的进行还是将密码都设置一样；

2、在实验环境的脚本都已经手动的赋予X执行权限；

3、要有一台模板机器，把要分发的文件准备好，然后只要使用expect脚本批量把需要同步的文件分发到目标机器即可；

4、所有脚本都在一个目录内/root/sync/，同时在写同步的文件时都要写绝对路径。


 实验脚本：

将一个文件内其列举的所有文件通过脚本一步同步到远程三台机器上：

```
[root@hpf-linuxsync]# cat ip.list 
192.168.1.222
192.168.1.2
192.168.1.20
	
[root@hpf-linuxsync]# cat movies.txt 
/root/cangls/cangls.avi
/root/longls/longls.avi
/root/xiaozels/xiaozels.avi
```
```
[root@hpf-linuxsync]# cat rsync.expect 
#!/usr/bin/expect
set passwd "***********"
set host [lindex $argv 0]
set file [lindex $argv 1]
spawn rsync -av --files-from=$file / root@$host:/tmp
expect {
"yes/no" { send "yes\r"}
"password:" { send "$passwd\r" }
}
expect eof
[root@hpf-linux sync]# cat rsync.sh 
#!/bin/bash
for ip in `cat ip.list`
do
    echo $ip
    ./rsync.expect $ip movies.txt
done

```
        上述实验需要注意的是rsync -av --files-from=$file / root@$host:/tmp这条命令的意思：根据一个文档中的文件列表来同步文件。同时另外三台机器的都要安装rsync，同时在movies.txt内的文件路径都要有真实的文件。


在一台机器上通过执行脚本可以在多台机器上进执行已在脚本自定义的命令：

```	
[root@hpf-linux sync]# cat exe.expect 
#!/usr/bin/expect
set host [lindex $argv 0]
set passwd "**************"
set cm [lindex $argv 1]
 
spawn ssh root@$host   
 
expect {
"yes/no" { send "yes\r"}
"password:" { send "$passwd\r" }
}
expect "]*"
send "$cm\r"
expect "]*"
send "exit\r"

```

``` 
[root@hpf-linux sync]# cat exe.sh 
#!/bin/bash
for ip in `cat ip.list`
do
    echo $ip
    ./exe.expect $ip "> /tmp/12.txt;date >> /tmp/12.txt;"
done

```
        上面的实验比较简单的在一台机器上通过自定义脚本可以向多台机器进行操作，这个脚本还有可以扩展的部分就是执行的命令可以专门写个脚本在进行嵌套循环执行，我在做这个实验时在脚本 exe.sh 中无法在添加命令了，一添加在执行脚本时就会出错，同时自己也尝试的写了执行命令的脚本，但由于能力有线就没能做出自己想要的结果，今后若将shell中for循环嵌套的脚本学会那就在尝试的完善这个实验。




批量操作服务器

```
#!/bin/bash
passwd='hadoop'

for item in node1 node2 node3;do
        /usr/bin/expect <<-EOF
        set timeout -1
        spawn ssh root@$item
        expect {
                "*yes/no*" {send "yes\r";exp_continue}
                "*password*" {send "$passwd\r"}
        }
        expect "*#"

        send "cd /root/guan\r"
        expect "*#"
        send "echo abcdefdsafa 113223243pz >> abc.md\r"
        expect "*#"
        send "exit\r"
        interact
        expect eof
EOF
done

```


**注意** 

```
执行此test.shell报如下错：

warning: here-document at line 17 delimited by end-of-file (wanted `EOF')

原因是末尾的EOF后面带有空格，EOF前后都不应有空格或其他符号

```

批量安装expect

```
#!/bin/bash

passwd = 'hadoop'

for item in node1 node2 node3;
do
    /usr/bin / expect << -EOF
set timeout - 1
spawn ssh root @$item
expect {
    "*yes/no*" { send "yes\r";
        exp_continue }
    "*password*" { send "$passwd\r" }
}

expect "*#"

send "yum install -y expect"

send "exit\r"
interact
expect eof
EOF
done

```


批量下载

```
#!/bin/bash
passwd='hadoop'

for item in node1 node2 node3;do
        /usr/bin/expect <<-EOF
        set timeout -1
        spawn ssh root@$item
        expect {
                "*yes/no*" {send "yes\r";exp_continue}
                "*password*" {send "$passwd\r"}
        }
		 expect "*#"
		 send "mkdir -p /root/temp\r"
		 expect "*#"
		 send "wget  wget http://192.168.25.120/soft/jdk-7u75-linux-x64.tar.gz -P /root/temp\r"

		 expect "*#"

		 send "cd /root/temp\r"
		 expect "*#"
		 send "tar xzvf jdk-7*\r"
		 expect "*#"
        send "exit\r"
        interact
        expect eof
EOF
done

```



测试可用代码

批量从服务器上下载文件并解压（✨✨⭐️）

```
#!/bin/bash
passwd='hadoop'

for item in node1 node2 node3;do
        /usr/bin/expect <<-EOF
        set timeout -1
        spawn ssh root@$item
        expect {
                "*yes/no*" {send "yes\r";exp_continue}
                "*password*" {send "$passwd\r"}
        }
        expect "*#*"
        send "yum install -y wget\r"
        expect "*#*"

        send "mkdir -p /root/temp\r"
        expect "*#*"
        send "wget  wget http://192.168.25.120/soft/jdk-8u162-linux-x64.tar.gz -P /root/temp\r"

        expect "*#"

        send "cd /root/temp\r"
        expect "*#"
        send "tar xzvf jdk-8u162-linux-x64.tar.gz -C /usr/local/dev/\r"
        expect "*#"
        send "exit\r"
        interact
        expect eof
EOF
done
```

批量关闭tomcat脚本

```
#!/bin/bash
passwd='hadoop'

for item in node1 node2 node3;do
        /usr/bin/expect <<-EOF
        set timeout -1
        spawn ssh root@$item
        expect {
                "*yes/no*" {send "yes\r";exp_continue}
                "*password*" {send "$passwd\r"}
        }

        expect "*#*"
        send "cd /root/temp/apa*/bin\r"

        expect "*#*"
        send "./shutdown.sh\r"

        expect "*#"
        send "cd /root/temp\r"
        
        expect "*#"
        send "exit\r"
        interact
        expect eof
EOF
done
```


<!--
create time: 2018-02-03 18:25:46
Author: Alfred

This file is created by Marboo<http://marboo.io> template file $MARBOO_HOME/.media/starts/default.md
本文件由 Marboo<http://marboo.io> 模板文件 $MARBOO_HOME/.media/starts/default.md 创建
-->

