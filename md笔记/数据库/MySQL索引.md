# MySQL索引

## 1、概述

MySQL官方对索引的定义为：索引（Index）是帮助MySQL高效获取数据的数据结构。所以归根结底，索引依然是一种数据结构。索引能大大提升对数据的查询能力，但是，为了维持索引，对于表的更新速度就会有所降低。

## 2、B+Tree和B-Tree

B+Tree与B-Tree 的区别：

1. B-树的关键字和记录是放在一起的，叶子节点可以看作外部节点，不包含任何信息；B+树的非叶子节点中只有关键字和指向下一个节点的索引，记录只放在叶子节点中。　
2. 在B-树中，越靠近根节点的记录查找时间越快，只要找到关键字即可确定记录的存在；而B+树中每个记录的查找时间基本是一样的，都需要从根节点走到叶子节点，而且在叶子节点中还要再比较关键字。从这个角度看B-树的性能好像要比B+树好，而在实际应用中却是B+树的性能要好些。因为B+树的非叶子节点不存放实际的数据，这样每个节点可容纳的元素个数比B-树多，树高比B-树小，这样带来的好处是减少磁盘访问次数。尽管B+树找到一个记录所需的比较次数要比B-树多，但是一次磁盘访问的时间相当于成百上千次内存比较的时间，因此实际中B+树的性能可能还会好些，而且B+树的叶子节点使用指针连接在一起，方便顺序遍历（例如查看一个目录下的所有文件，一个表中的所有记录等），这也是很多数据库和文件系统使用B+树的缘故。 

为什么说B+树比B-树更适合实际应用中操作系统的文件索引和数据库索引？

1.  B+树的磁盘读写代价更低 。B+树的内部结点并没有指向关键字具体信息的指针。因此其内部结点相对B 树更小。如果把所有同一内部结点的关键字存放在同一盘块中，那么盘块所能容纳的关键字数量也越多。一次性读入内存中的需要查找的关键字也就越多。相对来说IO读写次数也就降低了。
2.  B+树的查询效率更加稳定 。由于非终结点并不是最终指向文件内容的结点，而只是叶子结点中关键字的索引。所以任何关键字的查找必须走一条从根结点到叶子结点的路。所有关键字查询的路径长度相同，导致每一个数据的查询效率相当。

## 3 、索引分类

### 3.1 聚簇索引与非聚簇索引

对于聚簇索引，表中数据的逻辑顺序和物理顺序是相同的，而对于非聚簇索引，逻辑顺序和物理顺序并不需要相同。聚簇索引更像字典里的按拼音查找，而非聚簇索引更像字典里的按部首或者笔画查找。

聚簇索引的好处：按照聚簇索引排列顺序，查询显示一定范围数据的时候，由于数据都是紧密相连，数据库不不用从多个数据块中提取数据，所以节省了大量的io操作。

注意：聚集索引由于索引叶节点就是数据页，所以如果想检查主键的唯一性，需要遍历所有数据节点才行，但非聚集索引不同，由于非聚集索引上已经包含了主键值，所以查找主键唯一性，只需要遍历所有的索引页就行（索引的存储空间比实际数据要少），这比遍历所有数据行减少了不少IO消耗。这就是为什么主键上创建非聚集索引比主键上创建聚集索引在插入数据时要快的真正原因。

### 3.2 索引分类

1. **单值索引**：即一个索引只包含单个列，一个表可以有多个单列索引。

   ```mysql
   -- 随表一起建索引：
   CREATE TABLE customer (id INT(10) UNSIGNED  AUTO_INCREMENT ,customer_no VARCHAR(200),customer_name VARCHAR(200),  PRIMARY KEY(id),  KEY (customer_name));  
   -- 单独建单值索引：
   CREATE  INDEX idx_customer_name ON customer(customer_name);  
   -- 删除索引：
   DROP INDEX idx_customer_name  on customer;
   ```

2. **唯一索引**：索引列的值必须唯一，但允许有空值。

   ```mysql
    -- 随表一起建索引：
   CREATE TABLE customer (id INT(10) UNSIGNED  AUTO_INCREMENT ,customer_no VARCHAR(200),customer_name VARCHAR(200),  PRIMARY KEY(id),  KEY (customer_name),  UNIQUE (customer_no));  
   -- 单独建唯一索引：
   CREATE UNIQUE INDEX idx_customer_no ON customer(customer_no);  
   -- 删除索引：
   DROP INDEX idx_customer_no on customer ;
   ```

3. **主键索引**：设定为主键后数据库会自动建立索引，innodb为聚簇索引。

   ```mysql
   -- 随表一起建索引：
   CREATE TABLE customer (id INT(10) UNSIGNED  AUTO_INCREMENT ,customer_no VARCHAR(200),customer_name VARCHAR(200),  PRIMARY KEY(id) );   CREATE TABLE customer2 (id INT(10) UNSIGNED   ,customer_no VARCHAR(200),customer_name VARCHAR(200),  PRIMARY KEY(id) );  
   -- 单独建主键索引：
   ALTER TABLE customer  add PRIMARY KEY customer(customer_no);   
   -- 删除建主键索引：
   ALTER TABLE customer  drop PRIMARY KEY ;   
   -- 修改建主键索引：必须先删除掉(drop)原索引，再新建(add)索引
   ```

4. **复合索引**：即一个索引包含多个列。

   ```mysql
   -- 随表一起建索引：
   CREATE TABLE customer (id INT(10) UNSIGNED  AUTO_INCREMENT ,customer_no VARCHAR(200),customer_name VARCHAR(200),  PRIMARY KEY(id),  KEY (customer_name),  UNIQUE (customer_name),  KEY (customer_no,customer_name)); 
   -- 单独建索引：
   CREATE  INDEX idx_no_name ON customer(customer_no,customer_name);  
   -- 删除索引：
   DROP INDEX idx_no_name  on customer ;
   ```


## 4、建立索引条件

需要建立索引

1. 主键自动建立唯一索引；
2. 频繁作为查询条件的字段应该创建索引；
3. 查询中与其它表关联的字段，外键关系建立索引；
4. 单键/组合索引的选择问题， 组合索引性价比更高；
5. 查询中排序的字段，排序字段若通过索引去访问将大大提高排序速度；
6. 查询中统计或者分组字段；

不需要建立索引：

1. 表记录太少；
2. 经常增删改的表或者字段；；
3. Where条件里用不到的字段不创建索引；
4. 过滤性不好的不适合建索引；

