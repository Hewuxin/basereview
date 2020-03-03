### mysql

[toc]

##### 1.mysql三范式

* 1NF 列具有原子性，不可再分， mysql天然满足 1NF  为什么
* 2NF  表中不能有完全重复的两条记录 通过设置主键auto_increment	
* 3NF  列之间不能存在依赖关系  1列不能通过某种方式由另一列计算得到

##### 2.mysql E-R模型   实体-关系数据模型，用于数据库设计

* 实体 描述客观事物的概念  矩形框  一个人，一本书
* 属性 实体具有的某种特性 椭圆  学生属性 ： 学号姓名，年龄
* 关系  实体之间的联系    菱形代表关系
  	1：1  
    	1：n
    	n:m

##### 3.增删查改语句 

* create table(column1 int not  null auto_increment primary key);
* insert into table(column1,column2,....)values(
* update table set column='' where 筛选条件;
* delete from table where 筛选条件;
* drop,delete与truncate的区别
* drop删除表，truncate清空表，delete删除表、drop和truncate不能回滚。

* 表结构的修改
  alter table add column int not null default "";

##### 4.sql 的select语句完整的执行顺序

* select *                                  7
* from table1                           ①
* inner join table2                    ②
* on table1.key = table2.key     3
* where 筛选条件                      ④
* group by 分组列表                  5
* having 分组后的筛选               6
* order by 排序                         8 
* limit  偏移  分页                      9      limit (page-1),size;

##### 5.连接查询

* 外连接
  * 左外连接   select * from table1 left join tabel2 on table1.key=table2.key;  左边全有
    
  * 右外连接   select * from table1 right join tabel2 on table1.key=table2.key;  右边全有
    
  * 全外连接   select * from A left join B on A.key=B.key union select * from A right join B on A.key=B.key:   
    	A B 无相交 select * from A left join B on A.key=B.key where B.key is null union  select * from A left right B on A.key=B.key where A.key is null;
* 内连接    select * from A inner join B  on A.key=B.key;

##### 6.常见约束

* not null   非空
* default    默认
* primart key  主键
* unique  唯一
* foreign key 外键
* 添加约束  alter table 表名 modify   列名 int not null;md
* 添加外键约束   alter table 表名 add foreign key(列名) references   表名(列名);

##### 7.视图  

* 一种虚拟存在的表，只保存sql逻辑，不保存查询结果，  视图修改不影响基本表
* 游标是对查询出来的结果集作为一个单元来有效的处理。游标可以定在该单元中的特定行，从结果集的当前行检索一行或多行。
        可以对结果集当前行做修改。一般不使用游标，但是需要逐条处理数据的时候，游标显得十分重要。
* 作用： 
  	重用 sql 语句 简化复杂的sql操作  保护数据提高安全性
* 创建视图   create view viewname as (select * from A);
* 查看创建视图语句   show create view;
* 查看已创建的视图   show tables;
* 使用视图   select * from viewname;
* 删除视图  drop view 	viewname;

##### 8.事务  

* 特性 ACID
  * 原子性   一旦开启事务 事务的操作要么全部执行，要么全部不执行
  * 一致性   事务必须使使用的数据从一个一致性状态转换为另一个一致性状态;
  * 隔离性  当前事务内部使用的数据  对于并发执行的其他事务应该是隔离的，  并发执行的各个事物之间不能相互干扰	
  * 持久性  一旦事务commit 则其所作的修改会永久的保存到数据库中，即使系统奔溃修改的数据也不会丢失。
* 开启事务 start transaction
* 提交事务  commit  
* 回滚 rollback

##### 9.事务并发执行，如果没有采取必要的隔离机制会产生什么问题?

* 脏读    一个事物读取了另一个事务 未提交的数据。
* 不可重复度 ：  一个事务两次读取之间 另一个事务修改了数据 导致 两次数据不同。
* 幻读     一个事务两次读取同一张表之间 另一个事务加入或删除了数据 导致两次读取数据条数不同

##### 10.mysql 隔离级别?

* read uncommitted    三种并发问题都会出现
* read committed	解决了脏读   
* repeatable read	解决了 脏读和不可重复读
* serializable	解决了脏读、不可重复读、幻读  但性能下降严重 
* mysql 默认使用 repeatable read
* 查看隔离级别 select @@tx_isolation
* 设置隔离级别  set session  transaction isolation level 隔离级别;

##### 11.mysql 引擎都有哪些 InnoDB 和 MYISAME 区别

* myIsam ： 不支持主外键，不支持事务，有表锁(即使操作一条数据也会锁住整张表)，不适合高并发的操作，
  	   只缓存索引，不缓存真实数据， 表空间较小，关注读性能。

* InnoDB  支持主外键，支持事务，有行锁(操作时只锁一行，不影响其他行)适合高并发,
  	   不仅缓存索引还要缓存真实数据，对内存要求较高，而且内存大小对性能有决定性影响
  	   表空间较大，关注事务，mysql默认引擎。

##### 12.索引实现原理

* 索引分类：
  * 索引是帮助mysql高效获取数据的数据结构，排好序的快速查找数据结构.
  * 目的 提高查找效率，
  * 索引的实现通常使用 B树和B+树，索引加速了数据访问，因为存储引擎不会再去扫描整张表得到需要的数据；相反它从根节点开始，根节点保存了子节点的指针，存储引擎会根据指针快速寻找数据。	
  
    最常见的搜索结构是BST和AVL 然后无论是BST和AVL， 当数据量过大时都会由于树的深度过大而造成I/O读写过于频繁，进而导致查询效率低下，因此对于索引而言，B树(平衡多路查找树)各操作能使B树保持较低的高度，从而保证高效的查找效率

* 索引的优点和缺点
  * 优点： 1.提高数据检索的效率，降低数据库I/O成本
  * 2.降低数据排序的成本，降低CPU消耗
  * 缺点：1.时间方面： 创建和维护索引需要耗费时间，具体地对表中的数据进行增加、删除和修改的时候，索引也要动态的维护。

  * 空间方面：索引需要占用物理空间

##### 13.B树和B+树   

* B树特点   

  * 叶节点具有相同的深度，叶节点的指针为空
  * 所有索引元素不重复
  * 节点中的数据索引从左到右递增排列
  * 节点即存索引又存数据地址
 * B+树  B树的变种  多叉平衡树

     * 非叶子节点不存储data，只存储索引(冗余)
     * 叶子节点包含所有索引字段
     * 叶子节点用指针连接 提高区间访问性能
* InnoDB的索引使用的是B+树实现，B+树比B树的好处：
     * IO次数少: B+树的中间节点只存放索引，数据都存在叶节点中，

     * 因此中间节点可以存更多的数据，让索引树更加矮胖

     *  范围查询效率提高：B树需要中序遍历整个树，B+树只需要遍历也节点中的链表；

     * 查询效率更加稳定：每次查询都需要从根节点到叶节点，路径长度相同，
         所以每次查询的效率都差不多

##### 14.索引分类:

* 1.Btree索引  

* 2.Hash 索引   缺点范围查询效率相对较低
* 3.full-text 全文索引
* 4.R-tree 索引	

* 聚集索引 ： 索引和数据一起存储  (主键索引) InnoDB
* 非聚集索引： 索引和数据分开存储   	MyISam

* 单值索引 一个索引包含一个列， 一个表可以有多个单值索引(最多不要超过5个)
* 唯一索引  索引列的值必须唯一，但允许有空值
* 复合索引 一个索引包含多个列

* 创建、查看、删除索引
  * 创建索引
    create index  index_name on tablename(column1,column2...);
    alter table tablename add [unique] index index_name(column1,column2,...);
  * 查看索引:
    show index from table table_name;
  * 删除索引
    drop index index_name on table table_name;

##### 15.哪些情况需要创建索引 哪些情况不需要创建索引？

* 需要创建索引:
  * 主键自动创建主键索引		
  * 频繁查询的字段 可以创建索引
  * 查询中与其他表关联的字段，外键关系 建立索引
  * 经常出现在oder by/group by/ distinct 后面的字段
  * 在经常需要根据范围进行搜索的列上创建索引，因为索引连续，指定范围也是连续的

* 不要创建索引的情况:
  * 数据量小的时候 超过300w条数据 mysql性能才开始下降

  * 频繁更新的字段不要建立索引， 因为每次更信不单单是更新数据还要更新索引
  * 经常增删改的表   原因  降低更新表的速度   

  * where里用不到的字段不需要创建索引
  * 数据重复且分布平均的表字段	

  * 对数据量特别大的列text image， bit数据类型

##### 16.SQL性能下降的原因：

* 查询语句写的不好
* 索引失效(索引见了没用上)
* 关联查询太多join

* 服务器调优及参数设置(缓存，

##### 17.mysql常见瓶颈

* CPU：CPU在饱和的时候一般发生在数据装入内存或从磁盘上读取数据时候
  * <1. SQL问题， 如SQL中包含join，group by，order by  非索引字段条件查询等，增加了CPU运算的操作------》》SQL优化，建立合适的索引
  * <2. 单表数据量太大，查询时扫描的行太多，SQL效率低下，CPU率先出现瓶颈 ------>>水平分表 

* IO  磁盘IO瓶颈发生在装入数据远大于内存容量的时候   
  * 《1. 热点数据太多，数据库缓存放不下，每次查询时会产生大量的iO,降低查询速度  ---->>>分库和垂直分表
  * 《2。网络IO瓶颈，请求的数据太多，网络带宽不够  -------》》》分库
* 服务器硬件的性能瓶颈

##### 18分库分表

* 水平拆分数据，垂直拆分库或字段

* 水平分库  
  	以字段为依据，按照一定策略(hash，range等)，将一个库中的数据拆分到多个库中。
    	结果：
    		 每个库的结构都一样 

  ​			每个库的数据都不一样，没有交集  
  ​	场景：  系统并发量上来了分表难以解决，并且还没有明显的业务归属来垂直分库。  

  ​	库多了， io 和cpu的压力自然成倍减少

* 垂直分库 
  以表为依据，按照业务归属不同，将不同的表拆分到不同的库中。
  结果：
   1.每个库的结构不一样
   2.每个库的数据不一样，没有交集
   3.所有库的并集是全量数据
场景：  系统并发量上来了，并且可以抽象出单独的业务模块

* 水平分表
  以字段为依据，按照一定策略(hash,range等),将一个表中的数据拆分到多个表中
  结果：
    每个表的结构都一样
    每个表的数据都不一样，没有交集
    所有表的并集是全量数据
  场景：系统绝对并发量并没有上来，只是单表的数据量太多，影响SQL效率，加重CPU负担，以至于成为瓶颈
  分析：  单表的数据量减少，单次SQL执行效率高，自然减轻CPU的负担。
* 垂直分表 
  以字段为依据，按照字段的活跃性，将表中的字段拆到不同的表中(主表和扩展表）中。
  结果： 
    1.每个表的结构都不一样
    2.每个表的数据也不一样，一般来说，每个表至少有一列交集，一般是主键，用于关联数据
    3.所有表的并集是全量数据
  场景： 系统绝对并发没有上来，表的记录不多，但字段多，并且热点数据和非热点数据在一起，但行数据所需的存储空间较大。以至于数据库缓存的数据行减少，查询时去读磁盘数据产生大量的随机读IO，产生IO瓶颈。
  分析 ：将热点数据放在一起作为主表，非热点数据放在一起作为从表

##### 19.explain （执行计划)  

* 可以模拟优化器执行SQL查询语句，从而知道mysql是如何处理你的sql语句

* 分析你的查询语句或是表结构的性能瓶颈

* 能干什么：
  * 1.表的读取顺序
  * 2.数据读取操作的类型
  * 3.哪些索引可以被使用
  * 4.哪些索引实际被使用
  * 5.表之间的引用
  * 6.每张表有多少行被优化器查询

* 字段：
  	id 表的读取顺序
    	select_type  数据读取操作的操作类型
    	table 显示这一行数据是关于哪张表的

  ​	type 显示查询使用了哪种类型
  ​		**systyem>const>eq_ref>ref>range>index>all**
  ​		一般来说，得保证查询至少达到range级别，最好能达到ref
  ​		1.all 百万数据以上 type是all就需要优化了
  ​		2.system 表只有一行记录，这是const类型的特例
  ​		3. const 表示一次索引就找到
  ​		4.qe_ref 唯一性索引
  ​		5.ref 非唯一性索引扫描，返回匹配某个单独值的所有行
  ​		6.range 只检索给定范围的值
  ​		7. index index与all的区别为index只遍历索引树，
  ​	possible-keys  可能应用到这张表的索引，  但实际不一定用的到
  ​	key 实际用到的索引 null表示没有使用索引， 
  ​	ken_len 可以通过比较大小判断索引是否失效
  ​	ref  显示索引的哪一列被使用了，如果可能的话是一个常数，
  ​	extra 包含不适合在其他列中显示但是十分重要的额外信息
  ​		using file sort 说明mysql对数据使用了一个外部的索引排序，而不是按照表内的索引顺序进行读取，（会造成性能下降）
  ​		using temporary 使用临时表 保存中间结果 （会造成性能下降)

##### 20.覆盖索引

* 查询列和所建索引列相同
* select的数据列只用从索引中就能够取得，不必读取数据行，mysql可以利用索引返回列表中的字段，而不必根据索引再次读取数据文件
* 注意：如果要使用覆盖索引，一定要注意select列表中取出需要的列，不可select * ，将所有字段一起做索引会导致索引文件过大查询性能下降。

#### 21.索引优化

* 1.索引分析 explain select * from student where id = 1;
* range类型查询字段后面的索引无效
* 小表驱动大表
  * 左连接索引右加 因为左表数据全有，而右表数据相对较少
  * 右连接索引左加 因为右表数据全由而左表数据相对较少
* 三表  
  * explain select * from class left join book on class.card=book.card left join phon ob book.card=phone.card;
  * 索引最好设置在需要经常查询的字段中。
* join语句的优化
  * 1. 尽可能**减少join语句中的NestedLoop中的循环总次**数：**永远用小结果集驱动大的结果集**
  * 2. **优先优化NestedLoop的内层循环**：
  * 3. 保证**join语句中被驱动表上上join条件字段已经被索引；**
  * 4. 当**无法保证被驱动表的join条件字段被索引且内存资源充足的前提**下，不要太吝啬joinBuffer的设置

##### 索引失效

* **最佳左前缀**
* 覆盖索引： 即查找列于索引列的次序相同  
* **尽量使用全值匹配** 即使用与索引列相同的字段查找

 * **带头大哥不能死 ，**不能跳过索引字段的第一个字段 直接使用后面的字段索引查询；
 * **中间兄弟不能断，**即不能跳过中间的某个索引字段直接使用第一个索引字段和第三个索引字段查询；
 * **索引列上无计算**， 不能在索引列上做任何的计算(计算函数，手动或自动的类型转换)，都会导致索引失效而转向全表扫描
 * **like % 加右边**   即 使用 like模糊查询时  like ''%a' like 'a%b' 等都会造成索引失效
   	* 解决方法 使用 覆盖索引
* **范围之后全失效**    即 范围查询之后的索引字段全部会失效  
* **字符串里有引号**  字符串不加单引号会索引失效  
  * select * from student where name  =20;  底层隐式类型转换 int 转varchar

* 少用or  用它来连接时会索引失效

##### 一般性建议

* group by 基本上都需要排序 会有临时表产生(filesort, using temporary)
* 对于单值索引 尽量选择针对当前query国旅行更高的索引；
* 在选择组合索引的时候，当前query中过滤型性最好的字段在索引中的顺序越靠前越好。
* 在选择组合索引的时候，尽量选择能够包含当前query中where字段中更多字段的索引
* 尽可能通过分析统计信息和调整query的写法来达到选择合适索引的目的。

##### 分析

* 1观察，至少跑1天，看看生产慢的sql的情况
* 2开启慢查询日志，设置阈值，比如超过5秒的就是慢sql，并将它抓取出来
* 3 exlain + 慢SQL分析
* 4 show proflies
* 5 运维经理，DBA进行SQL数据库服务器的参数参数调优

##### 慢查询日志

* 1.mysql默认没有开启慢查询日志，需要我们手动来设置
* 2 不是调优需要一般不要启动该参数；
  * show variables like "%slow_query_log%";
  * set global slow_query_log=1;  开启慢查询
  * show variables like "long_query_time%";  # 查询时间
  * set  global long_query_time=3;   设置慢查询时间
  * show global status like "%slow_querires%";   
* show profiles 
  * 默认情况下关闭并保存最近15次的运行结果
  * **show variables like 'profiling';**
  * **set profiling=on;**
  * **运行SQL**
  * **show proflies;**
  * **show proflie cpu,block io for query3;**

##### 拖慢查询速度的原因

* **converting HEAP to myISam 查询结果太大，内存不够用往磁盘上搬**
* **creating tmp table table 创建临时表**
  * **拷贝数据到临时表**
  * **用完删除**
* **creating to tmp table on disk 把内存中临时表复制到磁盘  危险！**
* **locked 表被锁**





















​	






















​	






































​	