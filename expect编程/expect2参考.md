# expect2参考


```
#!/bin/bash
passwd="hadoop"
/usr/bin/expect << -EOF
set timeout -1
spawn ssh root@node1
#第一次登陆时。提示回答yes，然后是password，再次登陆直接就是password
expect {
"*yes/no" {send "yes\r";exp_continue}
"*assword*" {send "$passwd\r"}
}
expect "*#"
send "mkdir -p /root/temp\r"
expect "*#"
send "wget http://192.168.25.120/soft/jdk-7u75-linux-x64.tar.gz -P /root/temp\r"
expect "*#"
send "echo goodbay\r"
expect "*#"
send "exit\r"
expect eof
EOF
```


<!--
create time: 2018-07-19 18:58:00
Author: Alfred

This file is created by Marboo<http://marboo.io> template file $MARBOO_HOME/.media/starts/default.md
本文件由 Marboo<http://marboo.io> 模板文件 $MARBOO_HOME/.media/starts/default.md 创建
-->

