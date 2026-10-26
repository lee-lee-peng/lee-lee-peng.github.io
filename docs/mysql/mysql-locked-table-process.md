# MySQL查锁定的表并解锁

查open tables

```sql
SHOW OPEN TABLES WHERE in_use>0;
```

查Process List

```sql
SHOW FULL PROCESSLIST;
```

查Transactions

```sql
SELECT * FROM INFORMATION_SCHEMA.INNODB_TRX;
```

杀死进程

```sql
KILL process_ID/transaction_process_ID;
```