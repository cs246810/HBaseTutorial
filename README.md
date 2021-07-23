# Using Hbase with PostgreSQL

At first, you should download the binary which is compressed package.
```shell
$ wget https://mirrors.tuna.tsinghua.edu.cn/apache/hbase/2.4.4/hbase-2.4.4-bin.tar.gz
```
Now uncompress the package.
```shell
$ tar zxvf hbase-2.4.4-bin.tar.gz
```

Find where the JDK.
```shell
$ /usr/libexec/java_home -V
Matching Java Virtual Machines (2):
    1.8.291.10 (x86_64) "Oracle Corporation" - "Java" /Library/Internet Plug-Ins/JavaAppletPlugin.plugin/Contents/Home
    1.8.0_291 (x86_64) "Oracle Corporation" - "Java SE 8" /Library/Java/JavaVirtualMachines/jdk1.8.0_291.jdk/Contents/Home
/Library/Internet Plug-Ins/JavaAppletPlugin.plugin/Contents/Home
```

I could find my JDK path: `/Library/Java/JavaVirtualMachines/jdk1.8.0_291.jdk/Contents/Home`

Now set the `JAVA_HOME` environment.
```shell
$ export JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk1.8.0_291.jdk/Contents/Home
```

Start hbase by a convenient(便利的) way.
```shell
$ ./hbase-2.4.4/bin/start-hbase.sh
```

Verify that you have one running process called HMaster.
```shell
$ jps
4918 HMaster
70550 
5048 Jps
```

Connect to HBase.
```shell
$ ./hbase-2.4.4/bin/hbase shell
2021-07-23 06:20:44,681 WARN  [main] util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
HBase Shell
Use "help" to get list of supported commands.
Use "exit" to quit this interactive shell.
For Reference, please visit: http://hbase.apache.org/2.0/book.html#shell
Version 2.4.4, r20e7ba45b0c3affdc0c06b1a0e5cbddd1b2d8d18, Mon Jun  7 15:31:55 PDT 2021
Took 0.0077 seconds                                                                                                                                                                                  
hbase:001:0>
```

Create a table. 

Use the `create` command to create a new table. You must specify the table name and the ColumnFamily name.
```shell
$ ./hbase-2.4.4/bin/hbase shell
2021-07-23 06:20:44,681 WARN  [main] util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
HBase Shell
Use "help" to get list of supported commands.
Use "exit" to quit this interactive shell.
For Reference, please visit: http://hbase.apache.org/2.0/book.html#shell
Version 2.4.4, r20e7ba45b0c3affdc0c06b1a0e5cbddd1b2d8d18, Mon Jun  7 15:31:55 PDT 2021
Took 0.0077 seconds                                                                                                                                                                                  
hbase:001:0> create 'test', 'cf' 
Created table test
Took 2.1558 seconds                                                                                                                                                                                  
=> Hbase::Table - test
hbase:003:0>
```

List information about your table.

Use the `list` command to confirm your table exits.
```shell
$ ./hbase-2.4.4/bin/hbase shell
2021-07-23 06:20:44,681 WARN  [main] util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
HBase Shell
Use "help" to get list of supported commands.
Use "exit" to quit this interactive shell.
For Reference, please visit: http://hbase.apache.org/2.0/book.html#shell
Version 2.4.4, r20e7ba45b0c3affdc0c06b1a0e5cbddd1b2d8d18, Mon Jun  7 15:31:55 PDT 2021
Took 0.0077 seconds                                                                                                                                                                                  
hbase:001:0> create 'test', 'cf' 
Created table test
Took 2.1558 seconds                                                                                                                                                                                  
=> Hbase::Table - test
hbase:003:0> list 'test'
TABLE                                                                                                                                                                                                
test                                                                                                                                                                                                 
1 row(s)
Took 0.0851 seconds                                                                                                                                                                                  
=> ["test"]
hbase:004:0>
```

Use the `describe` command to see details.
```shell
hbase:005:0> describe 'test'
Table test is ENABLED                                                                                                                                                                                
test                                                                                                                                                                                                 
COLUMN FAMILIES DESCRIPTION                                                                                                                                                                          
{NAME => 'cf', BLOOMFILTER => 'ROW', IN_MEMORY => 'false', VERSIONS => '1', KEEP_DELETED_CELLS => 'FALSE', DATA_BLOCK_ENCODING => 'NONE', COMPRESSION => 'NONE', TTL => 'FOREVER', MIN_VERSIONS => '0
', BLOCKCACHE => 'true', BLOCKSIZE => '65536', REPLICATION_SCOPE => '0'}                                                                                                                             

1 row(s)
Quota is disabled
Took 0.3103 seconds
```

Put data into your table.
```shell
hbase:006:0> put 'test', 'row1', 'cf:a', 'value1'
Took 0.2298 seconds                                                                                                                                                                                  
hbase:007:0> put 'test', 'row2', 'cf:b', 'value2'
Took 0.0448 seconds                                                                                                                                                                                  
hbase:008:0> put 'test', 'row3', 'cf:c', 'value3'
Took 0.0130 seconds  
```

Scan the table for all data at once.
```shell
hbase:009:0> scan 'test'
ROW                                                COLUMN+CELL                                                                                                                                       
 row1                                              column=cf:a, timestamp=2021-07-23T06:33:12.762, value=value1                                                                                      
 row2                                              column=cf:b, timestamp=2021-07-23T06:33:28.942, value=value2                                                                                      
 row3                                              column=cf:c, timestamp=2021-07-23T06:33:38.589, value=value3                                                                                      
3 row(s)
Took 0.1896 seconds     
```

Get single row of data.
```shell
hbase:010:0> get 'test', 'row1'
COLUMN                                             CELL                                                                                                                                              
 cf:a                                              timestamp=2021-07-23T06:33:12.762, value=value1                                                                                                   
1 row(s)
Took 0.0464 seconds 
```

Disable a table.

If you want to delete a table or change its settings, as well as in some other situations(情况)， you
need disable the table first.
```shell
hbase:011:0> disable 'test'
Took 0.4402 seconds 
```

Drop the table.
```shell
hbase:012:0> drop 'test'
Took 0.4353 seconds 
```

Exit the HBase shell.
```shell
hbase:013:0> exit
$
```

Stop HBase.
```shell
$ ./hbase-2.4.4/bin/stop-hbase.sh
stopping hbase ........
```

We understood how to start and stop a standalone instance of HBase.

对于开发人员，学到这里就已经足够了，笔者只考虑如何将数据存，查询，删，修改在HBase中。在数据分析的数据库里
我想，更多的是要记住数据的历史变更信息，从而才能看到变量的波动线。所以删除笔者认为，没有必要，修改也没有。
所以这里只大范围只考虑存，查询，这是重点，更新和删除可以次要考虑。

我们的标题是如何将postgresql数据库和hbase结合。看到这里貌似，这是一个假的标题，因为在hbase中有自己创建表
的方式。

笔者本来是准备学习ROS的，无奈墙内无法安装，所以才来玩这个。

接下来应该学习客户端驱动操作hbase创建表，存数据，查询数据，删除数据，更新数据的操作。

这里有一个[HappyBase](https://happybase.readthedocs.io/en/latest/).

Now install HappyBase on the command line.
```shell
$ pip install happybase
```

不过，通过上面的hbase官方的例子，还是不明白hbase到底有什么用，以及如何用在我自己的项目，记录出发地，目的地
，列车编号，日期，不同车厢的价格信息，剩余票信息，如果hbase解决补了我这个问题，我准备就把hbase给删掉。

创建一个railway表并增加如下列：

|  train_number   | src  | dst | go_day | hard_seat_num | hard_seat_pay 
|  ---            | ---  | --- | ---    |  ---          | ---
|  k8888          | 上海  | 北京 | 2021-7-22 | 21         | 480.5

要设计存储这样一个数据信息，需要借鉴下别人是怎么用hbase存信息的，这里有一个知乎的网友的回答：

    Hbase属于NoSQL数据库产品，也即NoSQL都是列式存储，为此你要进行分析的数据特点要转换成NoSQL数据库适合存储的数据，也即Key-Value刑，另外对你所描述的业务数据信息，不是特别清楚：
    
    “我们有一个系统，每天会从各个地方弄过来n多log，所有log都有共同的一个key（手机号码）。通过map  reduce将这些log汇总成一个json串，就可以得到这个手机的一些信息。然后将这些信息存在数据库中，每天更新，更新量在4kw左右。这些数据每 天还会用map reduce来做数据挖掘（总量在4亿）。
    
    ”大致可以梳理的信息：
    1.每天会获得大量的LOG日志信息；
    2.每个LOG文件的名称KEY为手机号码；
    3.利用hadoop运算，将每个LOG文件的信息拼接成JSON串格式，也即Key-Value；
    4.将获得的数据存储到数据库中，对于已经存储的Key数据（也即手机号码），即更新，每天更新量大概4KW条数；
    5.使用Map reduce进行数据挖掘的运算，当下数据总量是40KW条数；
    6.数据分析需要使用范围搜索，也即不根据KEY进行搜索，那么使用NoSQL产品的优势就不存在了，具体Hbase是否做这方面的支持，我不清楚，因为无具体使用的经验，只是粗略了解而已。

从这个信息笔者了解到，我必须以 `k8888:上海:北京` 为key来扩展信息结构。

## Hbase 优缺点

* 适合场景：单表超千万，上亿，且高并发！
* 不适合场景：主要需求是数据分析，比如做报表。数据量规模不大，对实时性要求高

## Hbase和火车票 

我感觉我选错了。

关系型数据库就是能够减少数据冗余，如果要关联很多表，数据规模一上来效率就会急剧下降，看这里，我直接把
关联的多个表的数据全都放到一张表里面，能够解决不用在A表里查到了id还要到B表里找这个id对应的列的值，这
里就很方便。

从这里还可以看出是单表的查询速度。而且适合并发，那么我将所有的火车票数据全都存到单张表里面，肯定会有大
突破。

容我算算，一共有1千万个出发地，目的地组合，还要算上列车编号，以及日期，假设每天增加一次数据，那么一天就要
增加1千万*多少个列车这么多数据，粗略的可以说，一天有几亿的数据量增加到这张表里。一个月之后有40-50亿的数据

不说别的反正1千万数据，起码有2-3G的硬盘空间，由于这里大量数据容易，1千万数据大概有20G的硬盘空间占据，20G
的硬盘空间占据的话，20 * 10，一天的数据量就是200个G。

拜托，我的电脑才120G硬盘，要不笔者还是撤吧。玩什么数据分析呢？这不是找虐吗？

所以，大数据这种东西，是企业玩的，个人玩不动，1天200G数据增加，一个月就是 200 * 30 = 6000G的数据，也就是
粗略的6个T。

6个T，好像也是玩的起。

然后跑数据分析，首先要将数据拉出来，然后再数据可视化，恐怕也没有这么大的内存去做这种事情吧？如果要计算1年
的火车票数据的变化情况，这无疑是在找虐。所以，大数据这种**智力武器**最终只有被企业掌握的份。咱们普通老百姓
玩不起。

幸好笔者这里只是为了根据火车票的剩余数，价格数，来推测全国的人流量信息。从而在生意上选择去哪里做生意，毕竟
人多的地方注定就有消费，有消费，就能赚差价，有差价就能空手套白狼。

言归正传，现在还是来考虑怎么将火车票实施到HBase中。

还记得我的数据是要存储这种信息：

|  train_number   | src  | dst | go_day | hard_seat_num | hard_seat_pay 
|  ---            | ---  | --- | ---    |  ---          | ---
|  k8888          | 上海  | 北京 | 2021-7-22 | 21         | 480.5

需要做下修改，变成HBase的样子：

<table border="1">
    <tr>
        <th colspan="4">train_info</th>
        <th colspan="2">seat_info</th>
    </tr>
    <tr>
        <td>train_number</td>
        <td>src</td>
        <td>dst</td>
        <td>go_day</td>
        <td>hard_seat_num</td>
        <td>hard_seat_pay</td>
    </tr>
    <tr>
        <td>k888</td>
        <td>上海</td>
        <td>北京</td>
        <td>2021-7-22</td>
        <td>21</td>
        <td>480.5</td>
    </tr>
</table>

这里要变成hbase中的表就要这么玩：
```shell
hbase:013:0> create 'railway', 'train_info', 'seat_info'
Created table railway
Took 0.6531 seconds                                                                                                                                                                                  
=> Hbase::Table - railway
```

前面已经创建了'railway'表，需要在创建之前禁用表，然后再删除掉。

这里需要再次分析一下，因为我们最终是要根据`train_number`, `src`, `dst`来确定数据的变化，只有这3个列是固定
不变的，那么我们可以将这3个数据作为一个索引，也就是行名。

然后就是把这个数据加进去：
```shell
hbase:015:0> put 'railway', 'k888:上海:北京', 'train_info:', 'k888:上海:北京:2021-7-22'
Took 0.1730 seconds 
hbase:016:0> scan 'railway'
ROW                                                COLUMN+CELL                                                                                                                                       
 k888:\xE4\xB8\x8A\xE6\xB5\xB7:\xE5\x8C\x97\xE4\xB column=train_info:, timestamp=2021-07-23T09:21:44.488, value=k888:\xE4\xB8\x8A\xE6\xB5\xB7:\xE5\x8C\x97\xE4\xBA\xAC:2021-7-22                     
 A\xAC                                                                                                                                                                                               
1 row(s)
Took 0.0737 seconds                                                                                                                                                                                  
hbase:017:0> put 'railway', 'k888:上海:北京', 'seat_info:hard_seat_num', 21
Took 0.0045 seconds       
hbase:019:0> put 'railway', 'k888:上海:北京', 'seat_info:hard_seat_pay', 480.5
Took 0.0045 seconds
```

现在，数据就全部存进去了。

然后再统计这张表里有多少行数据:
```shell
hbase:021:0> count 'railway'
1 row(s)
Took 0.0390 seconds                                                                                                                                                                                  
=> 1
```

只有1行，虽然我们put数据弄了3次，但是确实只有1行数据。这里很抽象，完全是反人类的方式。

然后有没有一种更简单的，一次put将所有数据都弄进去？ 我们来试试，这次我们弄这个数据弄进去，就是修改下车次编号:

<table border="1">
    <tr>
        <th colspan="4">train_info</th>
        <th colspan="2">seat_info</th>
    </tr>
    <tr>
        <td>train_number</td>
        <td>src</td>
        <td>dst</td>
        <td>go_day</td>
        <td>hard_seat_num</td>
        <td>hard_seat_pay</td>
    </tr>
    <tr>
        <td>k999</td>
        <td>上海</td>
        <td>北京</td>
        <td>2021-7-22</td>
        <td>21</td>
        <td>480.5</td>
    </tr>
</table>

```shell
hbase:023:0> put 'railway', 'k999:上海:北京', 'train_info:', 'k999:上海:北京:2021-7-22', 'seat_info:hard_seat_num', 21, 'seat_info:hard_seat_pay', 480.5

ERROR: wrong number of arguments (given 8, expected 4..6)

For usage try 'help "put"'
```

错误，可以看到不可以这样子操作。

这里，可以优化，因为我已经将`train_number`, `src`, `dst`放到了row这里，所以，train_info信息中不能可以不包含前3个信息。
这样可以省不少的硬盘存储空间。

最后我们的表变成了这么一种结构，一种人类比较容易，也比较直观的视角：

| key | go_day | seat_info
| --- | ---         | ---
|k888:上海:北京| 2021-7-22 | 这里就不写了

如果还是不懂，可以Email联系。

至此，我们的大脑也能更加认同了HBase是一个key/value类型的数据库。

知乎上有网友说:"Hbase的表设计是一种艺术"。笔者现在也有了这样一种微小的feel.

再记录一点关于查询的，我现在要查询这个车次的价格:
```shell
hbase:024:0> get 'railway', 'k888:上海:北京'
COLUMN                                             CELL                                                                                                                                              
 seat_info:hard_seat_num                           timestamp=2021-07-23T09:23:16.186, value=21                                                                                                       
 seat_info:hard_seat_pay                           timestamp=2021-07-23T09:24:35.950, value=480.5                                                                                                    
 train_info:                                       timestamp=2021-07-23T09:21:44.488, value=k888:\xE4\xB8\x8A\xE6\xB5\xB7:\xE5\x8C\x97\xE4\xBA\xAC:2021-7-22                                         
1 row(s)
Took 0.0384 seconds 
```

至此，有关HBase来做火车票统计的应用就已经宣告结束了。

## Conclunse

1. We learned how to install HBase on mac OSX with a standalone.
2. We have known how to add data in hbase shell.
3. We learned how to design table by the railway application.
4. And we known When we should use HBase, and not use it.

Thank you very much for reading. My English is not good, but I am practicing like coding.
