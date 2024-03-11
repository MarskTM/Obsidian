
<mark style="background: #FFB8EBA6;">Transaction is the group of **SQL** into a single execution unit</mark>. Each transaction begins with a specific job and <mark style="background: #D2B3FFA6;">ends when all the tasks in the group has been successfully completed</mark>. <mark style="background: #FFB86CA6;">If any of the tasks fail, the transaction fails.</mark> Therefor, a transaction has two results: **success** or **failure**. 

Example of a transaction to transfer $150 from  account A to account B:
```
1. read(A)
2. A:= A-150
3. write(A)
4. 
5. read(B)
6. B := B + 150
7. write(B)
```

In this above example if any function does read/write() have an error the transaction will cancel update data to memory. By that, account A and account B like nothing to be changed.

## Properties of Transaction
A database transaction, by definition, must have four properties is:
- **Atomicity**(Toàn vẹn): The outcome of a transaction can either be completely successful or completely unsuccessful. <mark style="background: #ADCCFFA6;">The whole transaction must be rolled back if one part of it fails. </mark>

- **Consistency** (Nhất quán): <mark style="background: #BBFABBA6;">Transactions maintain integrity restriction</mark> by moving the database from one valid state to another. <mark style="background: #BBFABBA6;">Can understand, the DB sate must be valid after the transaction. All contraints must be satisfied.</mark>

- **Isolation** (Cô Lập): <mark style="background: #D2B3FFA6;">Concurrent transactions are isolated from one another, assuring the accuracy of the data</mark>.

- **Durability** (Độ bền): Once a transaction is committed, its modifications remain in effect<mark style="background: #FFB86CA6;"> even in the event of a system failure</mark>. 

## How to run Transaction in SLQ?

![[database_transaction_intro.png]]

The Transactions start with the syntax " BEGIN; " then we write SQL queries like normal. After that if all query SQL is successful, we will " COMMIT; " them to make them permanent. Otherwise, if one of the queries SQL has failed, we will " ROLLBACK; " transaction. thus any changes made by the previous SQL query will be gone and the database stay the same as it was, before the transaction. 


**Note**: these statements cannot be used while creating tables and are only used with the DML Commands such as- [INSERT](https://www.geeksforgeeks.org/sql-insert-statement/), [UPDATE](https://www.geeksforgeeks.org/sql-update-statement/), and [DELETE](https://www.geeksforgeeks.org/sql-delete-statement/).

# Transaction Isolation Level

Isolation is one of four ACID properties of transaction. Where at highest level, a perfect Isolation ensure that all concurrent transactions not affect each other. 

<mark style="background: #ADCCFFA6;">There are server ways transactions can be interfered by other transactions running with it. This interference we call "Read Phenomena"</mark>.  We will see some Read Phenomena cases when running at Isolation low level.

| Read Phenomena            | Description                                                                                                                                                                                                                                              |
| ------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Dirty read**            | This happen when a transaction **reads** data written by other concurrent **uncommitted transaction**.                                                                                                                                                   |
| **Non-Repeatable Read**   | When the transaction **reads** the **same row twice** and sees different value because it has been **modified** by other **committed transaction**.                                                                                                      |
| **Phantom Read**          | Similarity Non-Repeatable Read but affect for query multiple rows instate one.  In this case, the same query **re-executes** to find rows that satisfy a condition and **sees a different** set of rows, due to changes by other committed transactions. |
| **Serialization Anomaly** | When the result of **group** of concurrent committed transaction is **impossible to achieve** if we try to **run** them **sequentially** in any order **without overlapping**.                                                                           |


## 4 Standard Isolation Levels

There 4 standard isolation levels was defined by American Nation Standards Institute - ANSI.
![[transaction_isolation_level.png]]

<mark style="background: #FFB86CA6;">Fun Fact: With the serializable level there are two different ways to executive between MySQL and PostgreSQL:</mark>

- MySQL: <mark style="background: #D2B3FFA6;">Use locking mechanism</mark> to prevent serialization Anomaly. The transaction must wait release lock by another transaction using.

- PostgreSQL: <mark style="background: #D2B3FFA6;">Use it's dependency mechanism checking</mark> to detect read phenomena and stop them by <mark style="background: #D2B3FFA6;">throwing out an error</mark>.

![[compare-msql-postgresql.png]]


![[Isolation-levels-relasion.png]]

