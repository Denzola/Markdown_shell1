hbase批量创建namespace

第一步生成创建命令：

脚本拼接生成创建命令

```
cat table_name.txt | while read tName

do

	echo "create '$tName',{NAME=>'cf',VERSION=>1,COMPRESSION=>'SNAPPY',REPLICATION_SCOPE=>1,BLOOMFILTER=>'ROW'},{NUMREGIONS => 2,SPLTIALGO => 'HexStringSplit'}" >> hbase_file.txt

done

```

批量创建namespace脚本如下：

echo "创建命令" | hbase shell

```
cat hbase_file.txt | while read tName

do
	echo "$tName" | hbase shell
	sleep 1s
done

```



附录：

soft a.txt >> b.txt

排序a文件的内容输出到b文件中


diff a.txt b.txt | c.txt




