## 1. 数据量限制

由于资源的有限性，腾讯对各种类型的 MySQL 实例做了数据量限制。下面从技术角度谈谈云数据库在大数据量下的单个实例以及单个表使用的影响：
- **大数据量表**：单表数据量过大后，MySQL 对单表资源的管理成本（数据、索引等）变更，将直接影响表的处理效率。
- **大数据量实例**：云数据库默认存储引擎是 Innodb，当实例中的数据及索引页都能被 Innodb 的 cache buffer 缓存住时，MySQL 实例能够支持很大的并发访问。如果实例数据量过大，会导致 cache buffer 频繁的数据换出换进，MySQL 瓶颈很快转到 IO 上，访问吞吐量直趋下降（例如，某云数据库实例本可以支持每秒 8000 次访问，当数据量为 cache buffer 大小的两倍时，仅能支持每秒 700 次左右的访问）。

## 2. 连接数限制
云数据库的连接数上限为 MySQL 的系统变量 max_connections，当云数据库实例连接数量超过 max_connections 时，新的连接将无法建立。
云数据库最大连接数默认值为实例内存 1/5，且最大不超过 10240, 最小不低于 800。例如实例规格为 8000 MB 内存时，默认最大连接数为 8000/5=1600。用户可以根据需要自行调整 max_connections 的值。
云数据库控制台可修改的 max_connections 最大为 10000，如需更大连接数，请提交工单申请，腾讯云会根据实例内存的使用情况审批。
但是，连接数越多，消耗系统资源也越多。如果连接数超过实际系统的负载承受能力范围，必然影响系统服务质量。
关于 max_connections 可以参考 [MySQL官方手册](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_max_connections)。 

## 3. 连接云数据库的 MySQL 客户端的版本限制

我们建议使用 CVM 系统自带的 MySQL 客户端和 lib 库，连接云数据库实例。

## 4. 操作限制

1. 请不要修改 MySQL 实例已有帐号的信息和权限，这个操作可能会令部分集群服务失效。
2. 创建库和表时建议统一使用 Innodb 引擎，这个选择能使实例在支持高访问的能力上有更好的表现。
3. 请不要修改、停止 master-slave 关系，这个操作可能会令热备失效。

## 5. 关于慢查询的说明
1. 对于使用 Linux 云服务器的开发者，可以通过云数据库导出工具获取慢查询日志，详情请参考 <a href="https://cloud.tencent.com/document/product/236/8464" target="_blank">数据离线迁移</a>。
2. 对于使用 Windows 云服务器的开发者，暂时不能直接获取慢查询日志。如果有需要，请提交工单联系我们获取慢查询日志文件。 

## 6. 云数据库的 binlog 保存时间说明
由于 MySQL binlog 会占用大量的存储空间，所云数据库只保存最近 5 天的 binlog。另外，如果 binlog 数据量增加太快，服务器磁盘存储不下 5 天的 binlog，会人工删除 binlog，释放空间。 

## 7. 字符集说明

云数据库与 MySQL 数据库一样，默认字符集编码格式是：latin1，即 ISO-8859-1 编码格式。
虽然云数据库支持默认字符集编码的设置，但我们还是建议您在创建表时，显式的指定表的编码，并在连接建立时指定连接的编码。
这样，您的应用将会有更好的移植性。
关于 MySQL 字符集的相关资源请参考 MySQL 官方手册。 
下面是修改云数据库字符集的方法：

1. 执行如下语句可修改云数据库实例的默认字符集编码：
```
SET @@global.character_set_client = utf8;
SET @@global.character_set_results = utf8;
SET @@global.character_set_connection = utf8;
SET @@global.character_set_server = utf8;
```
其中 @@global.character_set_server 10 分钟左右将自动同步到本机文件进行持久化（另外 3 个变量不会同步到本机文件），迁移或重启将保持设置后的值。

2. 执行如下语句可修改当前连接的字符集编码：
```
SET @@session.character_set_client = utf8;
SET @@session.character_set_results = utf8;
SET @@session.character_set_connection = utf8;
```
或者
```
SET names utf8;
```

3. 对于 PHP 程序，可通过以下函数设置当前连接的字符集编码：
```
bool mysqli::set_charset(string charset);
```
或者
```
bool mysqli_set_charset(mysqli link, string charset);
```
详细请参考 [MySQL 官方手册](https://dev.mysql.com/doc/connectors/en/apis-php-mysqli.set-charset.html)。 

4. 对于 Java 程序，可通过如下方式设置当前连接的字符集编码：
```
jdbc:mysql://localhost:3306/dbname?useUnicode=true&characterEncoding=UTF-8
```
详细请参考 [MySQL 官方手册](https://dev.mysql.com/doc/connectors/en/connector-j-reference-configuration-properties.html)。 


