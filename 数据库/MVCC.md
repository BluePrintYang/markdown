### [MySQL-InnoDB-MVCC多版本并发控制](https://segmentfault.com/a/1190000012650596)

**多版本并发控制**: 指的是一种提高并发的技术。

最早的数据库系统，只有读读之间可以并发，读写，写读，写写都要阻塞。引入多版本之后，只有写写之间相互阻塞，其他三种操作都可以并行，这样大幅度提高了InnoDB的并发度。

MySQL官方文档描述

> Internally, `InnoDB` adds three fields to each row stored in the database. 

-  A 6-byte `DB_TRX_ID` field indicates the transaction identifier for the last transaction that inserted or updated the row
- Each row also contains a 7-byte `DB_ROLL_PTR` field called the roll pointer
- A 6-byte `DB_ROW_ID` field contains a row ID that increases monotonically as new rows are inserted

> InnoDB向数据库中存储的每一行添加三个字段

- 6字节的`DB_TRX_ID`字段指示最后插入或更新该行的交易的交易标识符
- 每行还包含一个7字节的`DB_ROLL_PTR`字段，称为滚动指针
- 6字节的`DB_ROW_ID`字段包含一个行ID，该行ID随着插入新行而单调增加

**undo log**

Undo log是InnoDB MVCC事务特性的重要组成部分

