# DatabaseSecurity

### 1. 目前有哪些开源的和商用的数据库、包括非关系型的数据库？而不仅仅是局限于了解达梦和mysql；这些数据库中，相同类型(如关系数据库：Oracle、mysql、sql-server)之间的差异（还有非关系型数据库）是什么？

* 关系型数据库
```
Mysql数据库（美国，甲骨文公司）
Oracle数据库（美国，甲骨文公司）
Sql Server数据库（美国，微软公司）
DB2数据库（美国，IBM公司）
Access数据库（美国，微软公司）
PostgreSQL数据库（美国，加州大学伯克利分校）
SqLite数据库（一个团队）
OceanBase数据库（中国，阿里巴巴）
GaussDB数据库（中国，华为）
TDSQL数据库（中国，腾讯）
DM数据库 （中国，武汉达梦数据库公司）
```
* 非关系型数据库
```
MongoDb数据库 [划分：键值数据库]
CouchDB数据库 [划分：键值数据库]
Redis数据库 [划分：文档数据库]
Memcached数据库 [划分：文档数据库]
HBase数据库 [划分：列族数据库]
Neo4j数据库 [划分：图形数据库]
```
* Oracle、Sql Server、Mysql数据库之间的差别
```
1.定义：

Oracle 能在所有主流平台上运行（包括Windows）。完全支持所有的工业标准，采用完全开放策略，提供高可用性和高伸缩性的簇的解决方案。 
Oracle 在兼容性、可移植性、可联结性、高生产率上、开放性也存在优点。Oracle产品采用标准SQL，与 IBM SQL/DS，DB2，INGRES，IDMS/R等兼容。

SQL Server 具有使用方便、可伸缩性好、与相关软件集成程度高等优点，逐渐成为Windows平台下进行数据库应用开发较为理想的 选择之一。

MySQL是一种关系数据库管理系统，使用的 SQL 语言是用于访问数据库的最常用标准化语言。MySQL 软件采用了双授权政策，分为社区版和商业版，
其体积小、速度快、总体拥有成本低，开放源码。

Oracle和mysql都是甲骨文的产品，SQL Server 是微软的产品


2.面向对象：

Oracle:主流的大型数据库，用于中大型网站开发，商业收费

SQL Server：一般做中小型数据库，用于中小型网站，以及个人使用  ，商业收费

Mysql:一般做中小型数据库，用于中小型网站，以及个人使用，开源免费


3.架构：

Oracle： 数据文件包括：控制文件、数据文件、重做日志文件、参数文件、归档文件、密码文件。这是根据文件功能行进行划分，
并且所有文件都是二进制编码后的文件，对数据库算法效率有极大的提高。由于Oracle文件管理的统一性，就可以对SQL执行过程中的解析和优化，
指定统一的标准：RBO（基于规则的优化器）、CBO（基于成本的优化器）通过优化器的选择，以及无敌的HINT规则，
给与了SQL优化极大的自由，对CPU、内存、IO资源进行方方面面的优化。

MySQL：最大的一个特色，就是自由选择存储引擎。每个表都是一个文件，都可以选择合适的存储引擎。常见的引擎有 InnoDB、 MyISAM、 NDBCluster等。
但由于这种开放插件式的存储引擎，比如要求数据库与引擎之间的松耦合关系。从而导致文件的一致性大大降低。在SQL执行优化方面，
也就有着一些不可避免的瓶颈。在多表关联、子查询优化、统计函数等方面是软肋，而且只支持极简单的HINT。

SQL Server ：数据架构基本是纵向划分，分为：Protocol Layer（协议层）， Relational Engine（关系引擎）， Storage Engine（存储引擎），
SQLOS。SQL执行过程就是逐层解析的过程，其中Relational Engine中的优化器，是基于成本的（CBO），其工作过程跟Oracle是非常相似的。
在成本之上也是支持很丰富的HINT，包括：连接提示、查询提示、表提示。

4.安全性：

Oracle比SQL Server高，SQL Server又比MySQL高
```
***
### 2. 这些数据库有哪些功能，比如面向SQL标准，支持程度是怎样的？有哪些不同于标准的功能？数据的导入导出、备份恢复机制有哪些等等，都去使用一下？并且一定尝试一下多用户操作？
***
### 3. 关于数据库及其实现原理，数据库内部数据的存储机制、事务的概念（如隔离级）、ACID特性；数据库应该怎样设计才能保证数据冗余最少、数据一致性如何维护、如何使得数据库操作效率更高；复杂查询有哪些（如：子查询、带exist的查询、相关子查询），这些查询的查询计划是怎样的（有些数据库可以看到查询计划）、关系代数在数据库中是如何实现的；备份恢复机制是怎样的、并发操作的一致性是什么、并发操作的封锁和多版本机制是什么？
***
### 4. 下载安装开源的数据库源代码，研究数据库的实现机制，看看上述原理是怎样实现的。

***

```
数据库创建、建表、增删改查、缓冲区管理、崩溃恢复等模块中的函数的主要功能及处理的过程【比如
Buf_page_get()；buf_LRU_get_free_block()；
create_table_def()；dict_create_or_check_sys_tablespace()；
innobase_commit()；recv_recovery_from_checkpoint_start()；
row_sel()；row_search_mvcc()；row_ins()；row_upd_step()等】。
通过执行SQL语句进行跟踪观察。
```

#### * buf_page_get函数解析

这个函数极其重要，是其他模块获取数据页的外部接口函数。如果请求的数据页已经在Buffer Pool中了，修改相应信息后，
就直接返回对应数据页指针，如果Buffer Pool中没有相关数据页，则从磁盘中读取。Buf_page_get是一个宏定义，真正的函数为
buf_page_get_gen，参数主要为space_id, page_no, lock_type, mode以及mtr。这里主要介绍一个mode这个参数，
其表示读取的方式，目前支持六种，前三种用的比较多。

***BUF_GET:***
默认获取数据页的方式，如果数据页不在Buffer Pool中，则从磁盘读取，如果已经在Buffer Pool中，需要判断
是否要把他加入到young list中以及判断是否需要进行线性预读。如果是读取则加读锁，修改则加写锁。

***BUF_GET_IF_IN_POOL:***
只在Buffer Pool中查找这个数据页，如果在则判断是否要把它加入到young list中以及判断是否需要进行线性预读。
如果不在则直接返回空。加锁方式与BUF_GET类似。

***BUF_PEEK_IF_IN_POOL:*** 
与BUF_GET_IF_IN_POOL类似，只是即使条件满足也不把它加入到young list中也不进行线性预读。加锁方式与BUF_GET类似。

***BUF_GET_NO_LATCH:*** 
不管对数据页是读取还是修改，都不加锁。其他方面与BUF_GET类似。

***BUF_GET_IF_IN_POOL_OR_WATCH:*** 
只在Buffer Pool中查找这个数据页，如果在则判断是否要把它加入到young list中以及判断是否需要进行线性预读。
如果不在则设置watch。加锁方式与BUF_GET类似。这个是要是给purge线程用。

***BUF_GET_POSSIBLY_FREED:*** 
这个mode与BUF_GET类似，只是允许相应的数据页在函数执行过程中被释放，主要用在估算Btree两个slot之前的数据行数。

接下来，我们简要分析一下这个函数的主要逻辑。
```
首先通过buf_pool_get函数依据space_id和page_no查找指定的数据页在那个Buffer Pool Instance里面。
算法很简单instance_no = (space_id << 20 + space_id + page_no >> 6) % instance_num，
也就是说先通过space_id和page_no算出一个fold value然后按照instance的个数取余数即可。
这里有个小细节，page_no的第六位被砍掉，这是为了保证一个extent的数据能被缓存到同一个
Buffer Pool Instance中，便于后面的预读操作。

接着，调用buf_page_hash_get_low函数在page hash中查找这个数据页是否已经被加载到对应的
Buffer Pool Instance中，如果没有找到这个数据页且mode为BUF_GET_IF_IN_POOL_OR_WATCH
则设置watch数据页(buf_pool_watch_set)，

接下来，如果没有找到数据页且mode为BUF_GET_IF_IN_POOL、BUF_PEEK_IF_IN_POOL
或者BUF_GET_IF_IN_POOL_OR_WATCH函数直接返回空，表示没有找到数据页。
如果没有找到数据但是mode为其他，就从磁盘中同步读取(buf_read_page)。

在读取磁盘数据之前，我们如果发现需要读取的是非压缩页，则先从Free List中获取空闲的数据页，
如果Free List中已经没有了，则需要通过刷脏来释放数据页，这里的一些细节我们后续在LRU模块再分析，

获取到空闲的数据页后，加入到LRU List中(buf_page_init_for_read)。在读取磁盘数据之前，
我们如果发现需要读取的是压缩页，则临时分配一个buf_page_t用来做控制体，通过伙伴系统分配到
压缩页存数据的空间，最后同样加入到LRU List中(buf_page_init_for_read)。

做完这些后，我们就调用IO子系统的接口同步读取页面数据，如果读取数据失败，我们重试100次
(BUF_PAGE_READ_MAX_RETRIES)然后触发断言，如果成功则判断是否要进行随机预读
(随机预读相关的细节我们也在预读预写模块分析)。

接着，读取数据成功后，我们需要判断读取的数据页是不是压缩页，如果是的话，因为从磁盘中读取的压缩页的
控制体是临时分配的，所以需要重新分配block(buf_LRU_get_free_block)，把临时分配的buf_page_t
给释放掉，用buf_relocate函数替换掉，接着进行解压，解压成功后，设置state为BUF_BLOCK_FILE_PAGE，
最后加入Unzip LRU List中。

接着，我们判断这个页是否是第一次访问，如果是则设置buf_page_t::access_time，如果不是，
我们则判断其是不是在Quick List中，如果在Quick List中且当前事务不是加过Hint语句的事务，
则需要把这个数据页从Quick List删除，因为这个页面被其他的语句访问到了，不应该在Quick List中了。

接着，如果mode不为BUF_PEEK_IF_IN_POOL，我们需要判断是否把这个数据页移到young list中，
具体细节在后面LRU模块中分析。
接着，如果mode不为BUF_GET_NO_LATCH，我们给数据页加上读写锁。
最后，如果mode不为BUF_PEEK_IF_IN_POOL且这个数据页是第一次访问，则判断是否需要进行线性预读
(线性预读相关的细节我们也在预读预写模块分析)。
```

#### * buf_LRU_get_free_block函数解析
这个函数以及其调用的函数可以说是整个LRU模块最重要的函数，在整个Buffer Pool模块中也有举足轻重的作用。
如果能把这几个函数吃透，相信其他函数很容易就能读懂。
```
首先，如果是使用ENGINE_NO_CACHE发送过来的SQL需要读取数据，则优先从Quick List中获取(buf_quick_lru_get_free)。

接着，统计Free List和LRU List的长度，如果发现他们再Buffer Chunks占用太少的空间，则表示太多的空间被行锁，
自使用哈希等内部结构给占用了，一般这些都是大事务导致的。这时候会给出报警。

接着，查看Free List中是否还有空闲的数据页(buf_LRU_get_free_only)，如果有则直接返回，否则进入下一步。
大多数情况下，这一步都能找到空闲的数据页。
如果Free List中已经没有空闲的数据页了，则会尝试驱逐LRU List末尾的数据页。如果系统有压缩页，情况就有点复杂，
InnoDB会调用buf_LRU_evict_from_unzip_LRU来决定是否驱逐压缩页，如果Unzip LRU List大于LRU List的十分之一
或者当前InnoDB IO压力比较大，则会优先从Unzip LRU List中把解压页给驱逐，否则会从LRU List中把解压页和压缩页同时驱逐。
不管走哪条路径，最后都调用了函数buf_LRU_free_page来执行驱逐操作，这个函数由于要处理压缩页解压页各种情况，极其复杂。
大致的流程：首先判断是否是脏页，如果是则不驱逐，否则从LRU List中把链表删除，必要的话还从Unzip LRU List移走这个数据页
(buf_LRU_block_remove_hashed)，接着如果我们选择保留压缩页，则需要重新创建一个压缩页控制体，插入LRU List中，
如果是脏的压缩页还要插入到Flush List中，最后才把删除的数据页插入到Free List中(buf_LRU_block_free_hashed_page)。

如果在上一步中没有找到空闲的数据页，则需要刷脏了(buf_flush_single_page_from_LRU)，由于buf_LRU_get_free_block
这个函数是在用户线程中调用的，所以即使要刷脏，这里也是刷一个脏页，防止刷过多的脏页阻塞用户线程。

如果上一步的刷脏因为数据页被其他线程读取而不能刷脏，则重新跳转到上述第二步。进行第二轮迭代，与第一轮迭代的区别是，
第一轮迭代在扫描LRU List时，最多只扫描innodb_lru_scan_depth个，而在第二轮迭代开始，扫描整个LRU List。
如果很不幸，这一轮还是没有找到空闲的数据页，从三轮迭代开始，在刷脏前等待10ms。

最终找到一个空闲页后，page的state为BUF_BLOCK_READY_FOR_USE。
```
