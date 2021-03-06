## INFORMATION_SCHEMA
#information_schema 
information_schema数据库是MySQL系统自带的数据库，它提供了数据库元数据的访问方式。  
```sql
mysql> use information_schema;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> show tables;
+---------------------------------------+
| Tables_in_information_schema          |
+---------------------------------------+
| CHARACTER_SETS                        |
| COLLATIONS                            |
| COLLATION_CHARACTER_SET_APPLICABILITY |
| COLUMNS                               |
| COLUMN_PRIVILEGES                     |
| ENGINES                               |
| EVENTS                                |
| FILES                                 |
| GLOBAL_STATUS                         |
| GLOBAL_VARIABLES                      |
| KEY_COLUMN_USAGE                      |
| OPTIMIZER_TRACE                       |
| PARAMETERS                            |
| PARTITIONS                            |
| PLUGINS                               |
| PROCESSLIST                           |
| PROFILING                             |
| REFERENTIAL_CONSTRAINTS               |
| ROUTINES                              |
| SCHEMATA                              |
| SCHEMA_PRIVILEGES                     |
| SESSION_STATUS                        |
| SESSION_VARIABLES                     |
| STATISTICS                            |
| TABLES                                |
| TABLESPACES                           |
| TABLE_CONSTRAINTS                     |
| TABLE_PRIVILEGES                      |
| TRIGGERS                              |
| USER_PRIVILEGES                       |
| VIEWS                                 |
| INNODB_LOCKS                          |
| INNODB_TRX                            |
| INNODB_SYS_DATAFILES                  |
| INNODB_FT_CONFIG                      |
| INNODB_SYS_VIRTUAL                    |
| INNODB_CMP                            |
| INNODB_FT_BEING_DELETED               |
| INNODB_CMP_RESET                      |
| INNODB_CMP_PER_INDEX                  |
| INNODB_CMPMEM_RESET                   |
| INNODB_FT_DELETED                     |
| INNODB_BUFFER_PAGE_LRU                |
| INNODB_LOCK_WAITS                     |
| INNODB_TEMP_TABLE_INFO                |
| INNODB_SYS_INDEXES                    |
| INNODB_SYS_TABLES                     |
| INNODB_SYS_FIELDS                     |
| INNODB_CMP_PER_INDEX_RESET            |
| INNODB_BUFFER_PAGE                    |
| INNODB_FT_DEFAULT_STOPWORD            |
| INNODB_FT_INDEX_TABLE                 |
| INNODB_FT_INDEX_CACHE                 |
| INNODB_SYS_TABLESPACES                |
| INNODB_METRICS                        |
| INNODB_SYS_FOREIGN_COLS               |
| INNODB_CMPMEM                         |
| INNODB_BUFFER_POOL_STATS              |
| INNODB_SYS_COLUMNS                    |
| INNODB_SYS_FOREIGN                    |
| INNODB_SYS_TABLESTATS                 |
+---------------------------------------+
61 rows in set (0.00 sec)
```
### 0. 常用
1. [[SCHEMATA]]
2. [[TABLES]]
3. [[COLUMNS]]

### 1. 关于字符集和排序规则相关的系统表

CHARACTER_SETS ：存储数据库相关字符集信息（memory存储引擎）

COLLATIONS ：字符集对应的排序规则

COLLATION_CHARACTER_SET_APPLICABILITY：就是一个字符集和连线校对的一个对应关系而已

下面我们说一下character sets和collations的区别：

字符集（character sets）存储字符串，是指人类语言中最小的表义符号。例如’A'、’B'等；

排序规则（collations）规则比较字符串，collations是指在同一字符集内字符之间的比较规则

每个字符序唯一对应一种字符集，但一个字符集可以对应多种字符序，其中有一个是默认字符序(Default Collation)

MySQL中的字符序名称遵从命名惯例：以字符序对应的字符集名称开头；以_ci(表示大小写不敏感)、_cs(表示大小写敏感)或_bin(表示按编码值比较)结尾。例如：在字符序“utf8_general_ci”下，字符“a”和“A”是等价的

看一下有关于字符集和校对相关的MySQL变量：

character_set_server：默认的内部操作字符集

character_set_client：客户端来源数据使用的字符集

character_set_connection：连接层字符集

character_set_results：查询结果字符集

character_set_database：当前选中数据库的默认字符集

character_set_system：系统元数据(字段名等)字符集

再看一下MySQL中的字符集转换过程：

（1）. MySQL Server收到请求时将请求数据从character_set_client转换为character_set_connection；

（2）. 进行内部操作前将请求数据从character_set_connection转换为内部操作字符集，其确定方法如下：

使用每个数据字段的CHARACTER SET设定值；

若上述值不存在，则使用对应数据表的DEFAULT CHARACTER SET设定值(MySQL扩展，非SQL标准)；

若上述值不存在，则使用对应数据库的DEFAULT CHARACTER SET设定值；

若上述值不存在，则使用character_set_server设定值。

（3）. 将操作结果从内部操作字符集转换为character_set_results。

### 2. 权限相关的一些表

SCHEMA_PRIVILEGES：提供了数据库的相关权限，这个表是内存表是从mysql.db中拉去出来的。

TABLE_PRIVILEGES:提供的是表权限相关信息，信息是从 mysql.tables_priv 表中加载的

COLUMN_PRIVILEGES ：这个表可以清楚就能看到表授权的用户的对象，那张表那个库以及授予的是什么权限，如果授权的时候加上with grant option的话，我们可以看得到PRIVILEGE_TYPE这个值必须是YES。

USER_PRIVILEGES:提供的是表权限相关信息，信息是从 mysql.user 表中加载的

通过表我们可以很清晰看得到MySQL授权的层次，SCHEMA，TABLE，COLUMN级别，当然这些都是基于用户来授予的。可以看得到MySQL的授权也是相当的细密的，可以具体到列，这在某一些应用场景下还是很有用的，比如审计等。

### 3. 存储数据库系统的实体对象的一些表

COLUMNS：存储表的字段信息，所有的存储引擎

INNODB_SYS_COLUMNS ：存放的是INNODB的元数据， 他是依赖于SYS_COLUMNS这个统计表而存在的。

ENGINES ：引擎类型，是否支持这个引擎，描述，是否支持事物，是否支持分布式事务，是否能够支持事物的回滚点

EVENTS ：记录MySQL中的事件，类似于定时作业

FILES ：这张表提供了有关在MySQL的表空间中的数据存储的文件的信息，文件存储的位置，这个表的数据是从InnoDB in-memory中拉取出来的，所以说这张表本身也是一个内存表，每次重启重新进行拉取。也就是我们下面要说的INNODB_SYS_DATAFILES这张表。还要注意一点的是这张表包含有临时表的信息，所以说和SYS_DATAFILES 这张表是不能够对等的，还是要从INNODB_SYS_DATAFILES看。如果undo表空间也配置是InnoDB 的话，那么也是会被记录下来的。

PARAMETERS ：参数表存储了一些存储过程和方法的参数，以及存储过程的返回值信息。存储和方法在ROUTINES里面存储。

PLUGINS ：基本上是MySQL的插件信息，是否是活动状态等信息。其实SHOW PLUGINS本身就是通过这张表来拉取道德数据

ROUTINES：关于存储过程和方法function的一些信息，不过这个信息是不包括用户自定义的，只是系统的一些信息。

SCHEMATA：这个表提供了实例下有多少个数据库，而且还有数据库默认的字符集

TRIGGERS :这个表记录的就是触发器的信息，包括所有的相关的信息。系统的和自己用户创建的触发器。

VIEWS :视图的信息，也是系统的和用户的基本视图信息。

这些表存储的都是一些数据库的实体对象，方便我们进行查询和管理，对于一个DBA来说，这些表能够大大方便我们的工作，更快更方便的了结和查询数据库的相关信息。

### 4. 约束外键等相关的一些表

REFERENTIAL_CONSTRAINTS：这个表提供的外键相关的信息，而且只提供外键相关信息

TABLE_CONSTRAINTS ：这个表提供的是 相关的约束信息

INNODB_SYS_FOREIGN_COLS ：这个表也是存储的INNODB关于外键的元数据信息和SYS_FOREIGN_COLS 存储的信息是一致的

INNODB_SYS_FOREIGN ：存储的INNODB关于外键的元数据信息和SYS_FOREIGN_COLS 存储的信息是一致的，只不过是单独对于INNODB来说的

KEY_COLUMN_USAGE：数据库中所有有约束的列都会存下下来，也会记录下约束的名字和类别

为什么要把外键和约束单列出来呢，因为感觉这是一块独立的东西，虽然我们的生产环境大部分都不会使用外键，因为这会降低性能，但是合理的利用约束还是一个不错的选择，比如唯一约束。

### 5. 关于管理的一些的一些表

GLOBAL_STATUS ，GLOBAL_VARIABLES，SESSION_STATUS，SESSION_VARIABLES：这四张表分别记录了系统的变量，状态（全局和会话的信息），作为DBA相信大家也都比较熟悉了，而且这几张表也是在系统重启的时候回重新加载的。也就是内存表。

PARTITIONS ：MySQL分区表相关的信息，通过这张表我们可以查询到分区的相关信息（数据库中已分区的表，以及分区表的分区和每个分区的数据信息）

PROCESSLIST：show processlist其实就是从这个表拉取数据，PROCESSLIST的数据是他的基础。由于是一个内存表，所以我们相当于在内存中查询一样，这些操作都是很快的。

INNODB_CMP_PER_INDEX，INNODB_CMP_PER_INDEX_RESET：这两个表存储的是关于压缩INNODB信息表的时候的相关信息,有关整个表和索引信息都有.我们知道对于一个INNODB压缩表来说,不管是数据还是二级索引都是会被压缩的,因为数据本身也可以看作是一个聚集索引。

INNODB_CMPMEM ，INNODB_CMPMEM_RESET：这两个表是存放关于MySQL INNODB的压缩页的buffer pool信息，但是要注意一点的就是,用这两个表来收集所有信息的表的时候,是会对性能造成严重的影响的,所以说默认是关闭状态的。如果要打开这个功能的话我们要设置innodb_cmp_per_index_enabled参数为ON状态。

INNODB_BUFFER_POOL_STATS ：表提供有关INNODB 的buffer pool相关信息，和show engine innodb status提供的信息是相同的。也是show engine innodb status的信息来源。

INNODB_BUFFER_PAGE_LRU，INNODB_BUFFER_PAGE :维护了INNODB LRU LIST的相关信息

INNODB_BUFFER_PAGE ：这个表就比较屌了，存的是buffer里面缓冲的页数据。查询这个表会对性能产生很严重的影响，千万不要再我们自己的生产库上面执行这个语句，除非你能接受服务短暂的停顿

INNODB_SYS_DATAFILES ：这张表就是记录的表的文件存储的位置和表空间的一个对应关系(INNODB)

INNODB_TEMP_TABLE_INFO ：这个表惠记录所有的INNODB的所有用户使用到的信息，但是只能记录在内存中和没有持久化的信息。

INNODB_METRICS ：提供INNODB的各种的性能指数，是对INFORMATION_SCHEMA的补充，收集的是MySQL的系统统计信息。这些统计信息都是可以手动配置打开还是关闭的。有以下参数都是可以控制的：innodb_monitor_enable, innodb_monitor_disable, innodb_monitor_reset, innodb_monitor_reset_all。

INNODB_SYS_VIRTUAL :表存储的是INNODB表的虚拟列的信息，当然这个还是比较简单的，在MySQL 5.7中，支持两种Generated Column，即Virtual Generated Column和Stored Generated Column，前者只将Generated Column保存在数据字典中（表的元数据），并不会将这一列数据持久化到磁盘上；后者会将Generated Column持久化到磁盘上，而不是每次读取的时候计算所得。很明显，后者存放了可以通过已有数据计算而得的数据，需要更多的磁盘空间，与实际存储一列数据相比并没有优势，因此，MySQL 5.7中，不指定Generated Column的类型，默认是Virtual Column。

INNODB_CMP，INNODB_CMP_RESET：存储的是关于压缩INNODB信息表的时候的相关信息，详细请见推荐笔记。

为什么把这些表列为管理相关的表呢，因为我感觉像连接，分区，压缩表，innodb buffer pool等表，我们通过这些表都能很清晰的看到自己数据库的相关功能的状态，特别是我们通过一些变量更容易窥透MySQL的运行状态，方便我们进行管理。

### 6. 关于表信息和索引信息的一些表

TABLES，TABLESPACES，INNODB_SYS_TABLES ，INNODB_SYS_TABLESPACES ：

TABLES这张表毫无疑问了，就是记录的数据库中表的信息，其中包括系统数据库和用户创建的数据库。show table status like 'test1'\G的来源就是这个表；

TABLESPACES 却是标注的活跃表空间。 这个表是不提供关于innodb的表空间信息的，对于我们来说并没有太大作用，因为我们生产库是强制INNODB的；

INNODB_SYS_TABLES 这张表依赖的是SYS_TABLES数据字典中拉取出来的。此表提供了有关表格的格式和存储特性，包括行格式，压缩页面大小位级别的信息（如适用）

提供的是关于INNODB的表空间信息，其实和SYS_TABLESPACES 中的INNODB信息是一致的。

STATISTICS：这个表提供的是关于表的索引信息，所有索引的相关信息。

INNODB_SYS_INDEXES：提供相关INNODB表的索引的相关信息，和SYS_INDEXES 这个表存储的信息基本是一样的，只不过后者提供的是所有存储引擎的索引信息，后者只提供INNODB表的索引信息。

INNODB_SYS_TABLESTATS：

这个表就比较重要了，记录的是MySQL的INNODB表信息以及MySQL优化器会预估SQL选择合适的索引信息，其实就是MySQL数据库的统计信息

这个表的记录是记录在内存当中的，是一个内存表，每次重启后就会重新记录，所以只能记录从上次重启后的数据库统计信息。有了这个表，我们对于索引的维护就更加方便了，我们可以查询索引的使用次数，方便清理删除不常用的索引，提高表的更新插入等效率，节省磁盘空间。

INNODB_SYS_FIELDS ：这个表记录的是INNODB的表索引字段信息，以及字段的排名

INNODB_FT_CONFIG :这张表存的是全文索引的信息

INNODB_FT_DEFAULT_STOPWORD：这个表存放的是stopword 的信息,是和全文索引匹配起来使用的，和innodb的 INFORMATION_SCHEMA.INNODB_FT_DEFAULT_STOPWORD 是相同的，这个STOPWORD必须是在创建索引之前创建，而且必须指定字段为varchar。stopword 也就是我们所说的停止词，全文检索时，停止词列表将会被读取和检索，在不同的字符集和排序方式下，会造成命中失败或者找不到此数据，这取决于停止词的不同的排序方式。我们可以使用这个功能筛选不必要字段。

INNODB_FT_INDEX_TABLE：这个表存储的是关于INNODB表有全文索引的索引使用信息的，同样这个表也是要设置innodb_ft_aux_table以后才能够使用的，一般情况下是空的

INNODB_FT_INDEX_CACHE ：这张表存放的是插入前的记录信息，也是为了避免DML时候昂贵的索引重组

### 7. 关于MySQL优化相关的一些表

OPTIMIZER_TRACE ：提供的是优化跟踪功能产生的信息.

PROFILING：SHOW PROFILE可以深入的查看服务器执行语句的工作情况。以及也能帮助你理解执行语句消耗时间的情况。一些限制是它没有实现的功能，不能查看和剖析其他连接的语句，以及剖析时所引起的消耗。

SHOW PROFILES显示最近发给服务器的多条语句，条数根据会话变量profiling_history_size定义，默认是15，最大值为100。设为0等价于关闭分析功能。详细信息请见MySQL profile

INNODB_FT_BEING_DELETED,INNODB_FT_DELETED: INNODB_FT_BEING_DELETED 这张表是INNODB_FT_DELETED的一个快照,只在OPTIMIZE TABLE 的时候才会使用。

### 8. 关于MySQL事物和锁的相关的一些表

INNODB_LOCKS:现在获取的锁，但是不含没有获取的锁，而且只是针对INNODB的。

INNODB_LOCK_WAITS：系统锁等待相关信息，包含了阻塞的一行或者多行的记录，而且还有锁请求和被阻塞改请求的锁信息等。

INNODB_TRX：包含了所有正在执行的的事物相关信息（INNODB），而且包含了事物是否被阻塞或者请求锁。

我们通过这些表就能够很方便的查询出来未结束的事物和被阻塞的进程。

----
