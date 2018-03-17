# zookeeper自动搭建脚本【参考】
* 脚本在mac主机执行，先下载zookeeper包，解压，重命名解压后的文件夹。
* 确定将此目录放在桌面文件夹下。
* 循环向三台虚拟机：传送zookeeper文件夹；更新hosts文件内容，增三台虚拟机的ip及主机名；
* 配置zookeeper，创建data、logs文件夹；设置zoo.cfg配置文件中的dataDir、dataLogDir，增三台虚拟机的ip及端口号；配置zookeeper环境变量

```
#!/bin/sh
echo "download zookeeper..."
wget http://mirror.bit.edu.cn/apache/zookeeper/zookeeper-3.5.2-alpha/zookeeper-3.5.2-alpha.tar.gz
tar -zxvf zookeeper-3.5.2-alpha.tar.gz
mv zookeeper-3.5.2-alpha /Users/ocean/Desktop/zookeeper
echo "download complete!"

for i in 0 1 2
do
echo "server$i:copy zookeeper..."
scp -r /Users/ocean/Desktop/zookeeper root@172.16.29.13$i:/root
echo "server$i:copy complete!"

echo "server$i:update hosts| config zookeeper..."
ip1="172.16.29.130 mini1 mini1"
ip2="172.16.29.131 mini2 mini2"
ip3="172.16.29.132 mini3 mini3"
sv1="server.1=mini1:2888:3888"
sv2="server.2=mini2:2888:3888"
sv3="server.3=mini3:2888:3888"
dtlog="dataLogDir=/root/zookeeper/logs"
ssh root@172.16.29.13$i "source /etc/profile;
echo ${ip1} >> /etc/hosts;
echo ${ip2} >> /etc/hosts;
echo ${ip3} >> /etc/hosts;
mkdir zookeeper/data;mkdir zookeeper/logs;
cp zookeeper/conf/zoo_sample.cfg zookeeper/conf/zoo.cfg;
echo ${dtlog} >> /root/zookeeper/conf/zoo.cfg;
echo ${sv1} >> /root/zookeeper/conf/zoo.cfg;
echo ${sv2} >> /root/zookeeper/conf/zoo.cfg;
echo ${sv3} >> /root/zookeeper/conf/zoo.cfg;
sed -i 's/dataDir=\/tmp\/zookeeper/dataDir\/root\/zookeeper\/data/g' zookeeper/conf/zoo.cfg;
echo $[i+1] > zookeeper/data/myid;
echo export ZOOKEEPER_HOME=/root/zookeeper;
echo export PATH=\\\$PATH:\\\$ZOOKEEPER_HOME/bin >> /etc/profile;"
echo "server$i:update hosts| config zookeeper complete!"
done
echo "------------------End!-------------------"

```

<!--
create time: 2018-03-15 09:59:08
Author: Alfred

This file is created by Marboo<http://marboo.io> template file $MARBOO_HOME/.media/starts/default.md
本文件由 Marboo<http://marboo.io> 模板文件 $MARBOO_HOME/.media/starts/default.md 创建
-->

