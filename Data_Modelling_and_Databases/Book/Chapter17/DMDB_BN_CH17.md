# Data Modelling and Databases - Chapter 17 (Book)

- Author: Ruben Schenk
- Date: 27.05.2021
- Contact: ruben.schenk@inf.ethz.ch

# 17. Coping With System Failures

## 17.1 Issues and Models for Resilient Operation

### 17.1.1 Failure Modes

There are many things that can go wrong as a database is queried and modified. The following items are a catalog of the most important failure modes and what the DBMS can do about them.

#### Erroneous Data Entry

The principal technique for addressing data-entry errors (such as a phone number missing a digit) is to write constraints and triggers that detect data believed to be erroneous.

#### Media Failures

A local failure of a disk, one that changes only a bit or a few bits, can normally be detected by parity checks associated with the sectors of the disk. Head crashes, where the entire disk becomes unreadable. are generally handled by one or both of the following approaches:

1. Use one of the RAID schemes.
2. Maintain an `archive`, a copy of the database on a medium such as tape or optical disk.
3. Instead of an archive, one could keep redundant copies of the database online, distributed among several sites.

#### Catastrophic Failures

In this category are a number of situations in which the media holding the database is completely destroyed, for example due to a fire. The approach of keeping an archive or redundant, distributed copies, will also protect against a catastrophic failure.

#### System Failures

The processes that query and modify the database are called `transactions`. Each transaction has a `state`, which represents what has happened so far in the transaction.

`System failures` are problems that cause the state of a transaction to be lost. Typical system failures are power loss and software errors.

When main memory is lost, the transaction state is lost. That is, we can no longer tell what parts of the transaction, including its database modifications, were made. The principal remedy for the problems that arise due to a system error is logging of all database changes in a separate, nonvolatile log, coupled with recovery when necessary.

### 17.1.2 More About Transactions

In the typical embedded SQL system, transactions begin as soon as operations on the database are executed and end with an explicit `COMMIT` or `ROLLBACK` command.

A transaction must execute atomically, that is, all-or-nothing and as if it were executed at an instant in time. Assuring that transactions are executed correctly is the job of the `transaction manager`, a subsystem that performs several functions, including:

1. Issuing signals to the log manager.
2. ASsuring that concurrently executing transactions do not interfere with each other in ways that introduce errors.

### 17.1.3 Correct Execution of Transactions

A database has a `state`, which is a value for each of its elements. Intuitively, we regard certain states as `consistent`, and others as inconsistent. Consistent states satisfy all constraints of the database schema, such as key constraints and constraints on values. 

A fundamental assumption about transactions is:

- `The Correctness Principle`: If a transaction executes in the absence of any other transaction or system errors, and it starts with the database in a consistent state, then the database is also in a consistent state when the transaction ends.

### 17.1.4 The Primitive Operations of Transactions

In order to study the details of logging algorithms and other transaction-management algorithms, we need a notation that describes all the operations that move data between address spaces. The primitives we shall use are:

1. `INPUT(X)`: Copy the disk block containing database element $X$ to a memory buffer.
2. `READ(X, t)`: Copy the database element $X$ to the transaction's local variable $t$. More precisely, if the block containing database element $X$ is not in a memory buffer then first execute `INPUT(X)`.
3. `WRITE(X, t)`: Copy the value of local variable $t$ to database element $X$ in a memory buffer. More precisely, we might first need to execute `INPUT(X)`.
4. `OUTPUT(X)`: Copy the block containing $X$ from its buffer to disk.

## 17.2 Undo Logging

A `log`  is a file of `log records`, each telling something about what some transaction has done. Our first style of logging - `undo logging` - makes repairs to the database state by undoing the effects of transactions that may not have completed before the crash.  
Additionally, in this section we introduce the basic idea of log records, including the `commit` action and its effect on the database state and log.

### 17.2.1 Log Records

Imagine the log as a file opened for appending only. As transactions execute, the `log manager` has the job of recording in the log each important event. One block of the log at a time is filled with log records, each representing one of these events.

There are several forms of log record that are used with each of the types of logging we discuss in this chapter. These are:

1. $< \text{START } T >$ : This record indicates that transaction $T$ has begun.
2. $< \text{COMMIT } T>$ : Transaction $T$ has completed successfully and will make no more changes to database elements. However, we cannot in general be sure that the changes are already on disk when we see the $< \text{COMMIT } T>$ log record.
3. $< \text{ABORT } T$ : Transaction $T$ could not complete successfully.

For an undo log, the only other kind of log record we need is an `update record`, which is a triple $< T, \, X, \, v>$. The meaning of this record is: transaction $T$ has changed database element $X$, and its former value was $v$.

### 17.2.2 The Undo-Logging Rules

An undo log is sufficient to allow recovery from a system failure, provided transactions and the buffer manager obey two rules:

- $U_1$ : If transaction $T$ modifies database element $X$, then the log record of the form $<T, \, X, \, v>$ must be written to the disk *before* the new value of $X$ is written to disk.
- $U_2$ : If a transaction commits, then its $\text{COMMIT}$ log record must be written to disk only *after* all database elements changed by the transaction have been written to disk, but as soon thereafter as possible.

In order to force log records to disk, the log manager needs a `flush-log` command that tells the buffer manager to copy to disk any log blocks that have not previously been copied to disk or that have been changed since they were last copied.

The following figure shows an example of some actions and their log entries:

<img src="./Figures/DMDB_BN_Fig17-3.JPG" width="450px"/><br>

*Figure 17.3: Actions and their log entries.*

### 17.2.3 Recovery Using Undo Logging

Suppose now that a system failure occurs. The `recovery manager` must use the log to restore the database to some consistent state.

The first task of the recovery manager is to divide the transactions into committed and uncommitted transactions. If there is a log record $< \text{COMMIT } T>$, then by undo rule $U_2$ all changes made by transaction $T$ were previously written to disk.  
However, suppose that we find a $< \text{START } T>$ record on the log but no $< \text{COMMIT } T>$ record. Then there could have been some changes to the database made by $T$ that were written to disk before the crash, while other changes by $T$ either were not made, or were made in the main-memory buffers but not copied to disk. In this case, $T$ is an `incomplete transaction` and must be `undone`.

The recovery manager must scan the log from the end. As it travels, it remembers all those transactions $T$ for which it has seen a $< \text{COMMIT } T>$ record or an $< \text{ABORT } T>$ record. Also, if it sees a record $<T, \, X, \, v>$, then:

1. If $T$ is a transaction whose $\text{COMMIT}$ record has been seen, then do nothing.
2. OTherwise, $T$ is an incomplete transaction, or an aborted transaction. The recover manager must change the value of $X$ in the database to $v$, in case $X$ had been altered just before the crash.

After making these changes, the recovery manager must write a log record $< \text{ABORT } T$ for each incomplete transaction $T$ that was not previously aborted, and then flush the log.
