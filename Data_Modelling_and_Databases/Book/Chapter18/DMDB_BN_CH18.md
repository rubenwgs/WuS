# Data Modelling and Databases - Chapter 18 (Book)

- Author: Ruben Schenk
- Date: 26.05.2021
- Contact: ruben.schenk@inf.ethz.ch

# 18. Concurrency Control

The timing of individual steps of different transactions needs to be regulated in some manner. This regulation is the job of the `scheduler` component of the DBMS, and the genera process of assuring that transactions preserve consistency when executing simultaneously is called `concurrency control`.

We begin by studying how to assure that concurrently executing transactions preserve correctness of the database state. The abstract requirement is called `serializability`, and there is an important, stronger condition called `conflict-serializability` that most schedulers actually enforce.

## 18.1 Serial and Serializable Schedules

### 18.1.1 Schedules

A `schedule` is a sequence of the important actions taken by one or more transactions. When studying concurrency control, the important read and write actions take place in the main-memory buffers, not the disk.

Example: Let us consider two transactions and the effect on the database the execution of those transactions has. We shall assume that the only consistency constraint on the database state is that $A = B$. Since $T_1$ adds $100$ to both $A$ and $B$, and $T_2$ multiplies both $A$ and $B$ by $2$, we know that each transaction, run in isolation, will preserve consistency.  
The important actions of the transactions $T_1$ and $T_2$ are shown in Fig. 18.2:

<img src="./Figures/DMDB_BN_Fig18-2.PNG" width="300px"/><br>

*Figure 18.2: Two transactions.*

### 18.1.2 Serial Schedules

A schedule is `serial` if its actions consist of all the actions of one transaction, then all the actions of another transaction, and so on. No mixing of the actions is allowed.

Example: Fig. 18.3 shows one of the two possible serial schedules of the transactions in Fig. 18.2:

<img src="./Figures/DMDB_BN_Fig18-3.PNG" height="400px"/><br>

*Figure 18.3: Serial schedule in which $T_1$ precedes $T_2$.*

### 18.1.3 Serializable Schedules

In general, we say a schedule $S$ is `serializable` if there is a serial schedule $S'$ such that for every initial database state, the effect of $S$ and $S'$ are the same.

Example: Fig. 18.5 below shows a schedule of our previous example that is serializable but not serial. Since all consistent database states have $A = B = c$ for some constant $c$, it is not hard to deduce that in the schedule of Fig. 18.5, both $A$ and $B$ will be left with the value $2(c + 100)$, and thus consistency is preserved.

<img src="./Figures/DMDB_BN_Fig18-5.PNG" height="400px"/><br>

*Figure 18.5: A serializable, but not serial, schedule.*

On the other hand, consider the schedule of Fig 18.6 below, which is not serializable. The reason we can be sure it is not serializable is that it takes the consistent state $A = B = 25$ and leaves the database in an inconsistent state, where $A = 250$ and $B = 150$.

<img src="./Figures/DMDB_BN_Fig18-6.PNG" height="400px"/><br>

*Figure 18.6: A nonserializable schedule.*

### 18.1.4 The Effect of Transaction Semantics

To simplify the job of the scheduler, it is conventional to assume that:

- Any database element $A$ that a transaction $T$ writes is given a value that depends on the database state in such a way that no arithmetic coincidences occur.

### 18.1.5 A Notation for Transactions and Schedules

If we assume "no coincidences", then only the reads and writes performed by the transaction matter, not the actual values involved. Thus, we shall represent transactions and schedules by a shorthand notation, in which the actions are $r_T(X)$ and $w_T(X)$, meaning that transaction $T$ reads, or respectively writes, database element $X$. Moreover, since we shall usually name our transactions $T_2, \, T_2,... \,$ we adopt the convention that $r_i(X)$ and $w_i(X)$ are synonyms for $r_{T_i}(X)$ and $w_{T_i}(X)$, respectively.

Example: The transactions of Fig. 18.2 can be written as:

$$
T_1: \, r_1(A); \, w_1(A); \, r_1(B); \, w_1(B); \\
T_2: \, r_2(A); \, w_2(A); \, r_2(B); \, w_2(B);
$$

To make the notation precise:

1. An `action` is an expression of the form $r_i(X)$ or $w_i(X)$, meaning that transaction $T_i$ reads or writes, respectively, the database element $X$.
2. A `transaction` $T_i$ is a sequence of actions with subscript $i$.
3. A `schedule` $S$ of a set of transactions $\mathcal{T}$ is a sequence of actions, in which for each transaction $T_i$ in $\mathcal{T}$, the actions of $T_i$ appear in $S$ in the same order that they appear in the definition of $T_i$ itself. We say that $S$ is an `interleaving` of the actions of the transactions of which it is composed.