# Linux定时任务

cron是一个linux下 的定时执行工具，可以在无需人工干预的情况下运行作业。<p>
　　service crond start    //启动服务<p>
　　service crond stop     //关闭服务<p>
　　service crond restart  //重启服务<p>
　　service crond reload   //重新载入配置<p>
　　service crond status   //查看服务状态 </p>


要把cron设为在开机的时候自动启动，在 /etc/rc.d/rc.local 脚本中加入 /sbin/service crond start 即可

查看当前用户的crontab，输入 crontab -l；

编辑crontab，输入 crontab -e；

删除crontab，输入 crontab -r


用户所建立的crontab文件中，每一行都代表一项任务，每行的每个字段代表一项设置，它的格式共分为六个字段，前五段是时间设定段，第六段是要执行的命令段，格式如下

```
minute hour day month week command

minute： 表示分钟，可以是从0到59之间的任何整数。

hour：表示小时，可以是从0到23之间的任何整数。

day：表示日期，可以是从1到31之间的任何整数。

month：表示月份，可以是从1到12之间的任何整数。

week：表示星期几，可以是从0到7之间的任何整数，这里的0或7代表星期日。

command：要执行的命令，可以是系统命令，也可以是自己编写的脚本文件。

```

![](http://p2ehgqigv.bkt.clouddn.com/18-3-3/21156130.jpg)


在以上各个字段中，还可以使用以下特殊字符：

  *  星号（*）：代表所有可能的值，例如month字段如果是星号，则表示在满足其它字段的制约条件后每月都执行该命令操作。
 
  *  逗号（,）：可以用逗号隔开的值指定一个列表范围，例如，“1,2,5,7,8,9”
 
  *  中杠（-）：可以用整数之间的中杠表示一个整数范围，例如“2-6”表示“2,3,4,5,6”
 
  *  正斜线（/）：可以用正斜线指定时间的间隔频率，例如“0-23/2”表示每两小时执行一次。同时正斜线可以和星号一起使用，例如*/10，如果用在minute字段，表示每十分钟执行一次。


###crond服务

安装crontab： yum install crontabs

服务操作说明：

```
	service crond start      ## 启动服务    service crond stop       ## 关闭服务    service crond restart    ## 重启服务    service crond reload     ## 重新载入配置    service crond status     ## 查看 crontab 服务状态:    chkconfig crond --list   ## 查看 crontab 服务是否已设置为开机启动    chkconfig crond on       ## 加入开机自动启动
```


配置实例：

```
*/1 * * * * date >> /root/date.txt每分钟执行一次 date 命令
30 21 * * * /usr/local/etc/rc.d/httpd restart 每晚的 21:30 重启 apache。45 4 1,10,22 * * /usr/local/etc/rc.d/httpd restart 每月 1、10、22 日的 4 : 45 重启 apache。10 1 * * 6,0 /usr/local/etc/rc.d/httpd restart 每周六、周日的 1 : 10 重启 apache。0,30 18-23 * * * /usr/local/etc/rc.d/httpd restart 每天18 : 00至23 : 00之间每隔30分钟重启apache。

* 23-7/1 * * * /usr/local/etc/rc.d/httpd restart 晚上 11 点到早上 7 点之间，每隔一小时重启 apache

```

[参考链接](https://www.jianshu.com/p/0712c75e8392)<p>
[参考链接](https://www.cnblogs.com/intval/p/5763929.html)


<!--
create time: 2018-03-03 19:33:36
Author: Alfred

This file is created by Marboo<http://marboo.io> template file $MARBOO_HOME/.media/starts/default.md
本文件由 Marboo<http://marboo.io> 模板文件 $MARBOO_HOME/.media/starts/default.md 创建
-->

