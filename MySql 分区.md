#### 为表分区

分区将单个表的各部分分散到一个文件系统中。

用户所选择的划分数据的规则被称为分区函数（partitioningfunction），它可以是模量（modulu），与一组范围或值列表、一个内部哈希函数或一个线性哈希函数简单匹配。

表的不同行可以被分配给不同的物理分区，称为水平分区。



对一个表做分区有很多种方法。

1. RANGE：这种类型的分区根据落在给定范围内的列值，将行分配给分区。
2. LIST：类似于按RANGE分区，不同的是其分区是基于与一组离散值匹配的列来选择的。
3.  HASH：在这种类型的分区操作中，一个分区是根据用户定义的表达式返回的值来选择的，该表达式对插入到表的行中的列值进行操作。HASH函数可以包含任何在MySQL中具有非负整数值的有效表达式。
4.  KEY：这种类型的分区类似于HASH分区，只是它仅提供一个或多个列，而且MySQL服务器提供自己的哈希函数。这些列可以包含除整数值以外的其他值，因为MySQL提供的哈希函数保证不管列数据是什么类型，结果都为整数。

上述每一个分区类型都有一个扩展。RANGE的扩展为RANGECOLUMNS，LIST的扩展为LIST COLUMNS，HASH 的扩展为LINEAR HASH，KEY 的扩展为LINEAR KEY。

对于 [LINEAR] KEY、RANGE COLUMNS和LIST COLUMNS分区，分区表达式包含一个或多个列的列表。

在RANGE、LIST和[LINEAR] HASH分区中，分区的列的值被传递给分区函数，该函数返回一个整数值，表示该特定记录应该被存储在第几个分区。这个函数的返回值必须为既非常数也非随机数。



#####如何分区

可以在创建表时指定分区，也可以通过执行ALTER TABLE命令来指定分区。分区列应该是表中所有唯一键的一部分。

如果基于created_at列定义了分区，并且id是主键，则应该将create_at列作为PRIMARY KEY的一部分包含在内，即（id，created_at）。以下示例假定该表的外键没有被引用。



如果希望在MySQL 8.0中基于范围或时间间隔实施分区计划，有两种选择：

1. 按RANGE对表进行分区。对于分区表达式，应用在DATE、TIME或DATETIME 列上运行的函数，并返回一个整数值。
2.  使用DATE或DATETIME列作为分区列，通过RANGECOLUMNS对表进行分区。

##### RANGE分区

如果想要根据emp_no对employees表分区，并希望在一个分区中保留100，000名员工，可以这样创建分区：

```sql
CREATE TABLE employees(
	emp_no INT(11) NOT NULL,
	birth_date date Not NULL,
	PRIMARY KEY (emp_no)
)
PARTITION BY RANGE (emp_no)
(PARTITION p0 VALUES less than (100000) ENGINE = INNODB,
 PARTITION p1 VALUES less than (200000) ENGINE = INNODB,
 PARTITION p2 VALUES less than (300000) ENGINE = INNODB,
 PARTITION pmax VALUES less than MAXVALUE ENGINE = INNODB
);
```

所有emp_no小于100，000的员工将被划入分区p0，所有emp_no小于200，000和大于100，000的员工将被划入分区p1，依此类推。

添加分区或创建一个 MAXVALUE 分区



如果你想基于`birth_date`分区，可以使用`YEAR(birth_date)`函数作为分区表达式：

```sql
CREATE TABLE employees(
	emp_no INT(11) NOT NULL,
	birth_date date Not NULL,
	PRIMARY KEY (emp_no, birth_date)
)
PARTITION BY RANGE ( YEAR(birth_date) )
(PARTITION p1980 VALUES less than (1980) ENGINE = INNODB,
 PARTITION p1990 VALUES less than (1990) ENGINE = INNODB,
 PARTITION p2000 VALUES less than (2000) ENGINE = INNODB,
 PARTITION pmax VALUES less than MAXVALUE ENGINE = INNODB
);
```



分区函数`to_days()`返回自0000-01-01以来的天数，这是一个整数值：



如果希望将现有的一个表转换为分区的表，如果分区键不是主键的一部分，则需要删除（drop）主键，并将分区键作为主键和所有唯一键的一部分添加进来。否则，你将收到一条报错的消息`“ERROR 1503 （HY000）：A PRIMARY KEY mustinclude all columns in the table＇s partitioningfunction..”`。你可以这样做：

```sql
alert table employees drop primary key , add primary key (emp_no, birth_date);

alert table employees partition by pange  ( YEAR(birth_date) )
(PARTITION p1980 VALUES less than (1980) ENGINE = INNODB,
 PARTITION p1990 VALUES less than (1990) ENGINE = INNODB,
 PARTITION p2000 VALUES less than (2000) ENGINE = INNODB,
 PARTITION pmax VALUES less than MAXVALUE ENGINE = INNODB
);
```

删除分区如果希望删除分区，可以执行`REMOVE PARTITIONING `语句：

```sql
alert table employees remove partitioning;
```



##### `RANGE COLUMNS`分区

RANGE COLUMNS分区类似于RANGE分区，但是它允许使用基于多个列值的范围来定义分区。此外，你可以使用非整数类型的列来定义范围。RANGE COLUMNS分区与RANGE分区在以下几方面有很大的区别：

1.  RANGE COLUMNS不接受表达式，只接受列的名称。
2.  RANGE COLUMNS接受一个或多个列的列表。
3.  RANGE COLUMNS的分区列不限于整数列；字符串、DATE和DATETIME列也可以用作分区列。

在 RANGE COLUMNS 中可以直接使用 hire_date 列，而不是使用 to_days（）或year（）函数。



##### LIST和LIST COLUMNS分区

LIST分区与RANGE分区类似，其每个分区都是根据一组值列表中的一个列值的成员来定义和选择的，而不是在一组连续的值范围内进行。

你需要通过PARTITION BY LIST（＜expr＞）来定义它，其中expr是一个列值或基于列值的表达式，并返回一个整数值。

分区定义包含VALUES IN （＜value_list＞），其中value_list是一个用逗号分隔的整数列表，而不是VALUES LESS THAN（＜value＞）。

如果希望使用除整数以外的数据类型，可以使用LISTCOLUMNS。

与RANGE分区的情况不同，这里没有像MAXVALUE之类的catch-all，分区表达式期望的所有值都应该包含在PARTITION表达式中。

```sql
create table customer(
	customer_id int,
    zipcode int ,
    city varchar(256),
    primary key (customer_id, zipcode)
)
PARTITION BY LIST(zipcode)(
	PARTITION pnorth VALUES IN (560030,560007,560051),
	PARTITION peast VALUES IN (560040,560008,560061),
	PARTITION pwest VALUES IN (560050,560009,560051)
)
```

如果希望直接使用列而不是整数，则可以使用LIST COLUMNS来分区：

```sql
create table customer(
	customer_id int,
    zipcode int ,
    city varchar(256),
    primary key (customer_id, city)
)
PARTITION BY LIST COLUMNS(city)(
	PARTITION pnorth VALUES IN ('city1','city2','city3'),
	PARTITION peast VALUES IN ('city4','city5','city6'),
	PARTITION pwest VALUES IN ('city7','city8','city9')
)
```

##### HASH和LINEAR HASH分区

HASH分区主要是为了确保数据均匀地分布在数量预先确定的一组分区中。使用RANGE或 LIST 分区的话，必须明确指定应该将给定的列值或列值集合存储在哪一个分区中；而如果使用HASH 分区，这个决定将由你来做，你只需要根据要进行哈希的列值指定一个列值或表达式，以及分区表要分为多少个分区即可。

如果你希望员工在分区中均匀地分布，可以指定分区的数量，并根据YEAR（hire_date）进行HASH分区，而不是根据YEAR（hire_date）进行RANGE分区。当使用 PARTITIONBY HASH 时，存储引擎会根据该**表达式结果的模**来确定要使用哪一个分区。

```sql
CREATE TABLE employees(
	emp_no INT(11) NOT NULL,
	birth_date date Not NULL,
	PRIMARY KEY (emp_no, birth_date)
)
PARTITION BY HASH ( YEAR(birth_date) )
PARTITIONS 8;
```



在 LINEAR HASH 分区中，可以使用相同的语法，只不过要添加 LINEAR 关键字。MySQL不使用MODULUS操作，而是使用2的幂算法来确定分区。

##### KEY和LINEAR KEY分区

KEY分区与HASH分区类似，不同之处在于，HASH分区使用用户定义的表达式，KEY分区的哈希函数由MySQL服务器提供。这个内部哈希函数采用的是与PASSWORD（）函数相同的算法。

KEY仅包含零个或几个列名称的列表。如果表有主键的话，则用作KEY分区的任何列都必须是主键的一部分或全部。如果没有列名可以被指定为分区键，则使用表的主键（如果有的话）：

##### 子分区

子分区你可以将每个分区进一步划分为一个分区表，称为子分区或复合分区：

