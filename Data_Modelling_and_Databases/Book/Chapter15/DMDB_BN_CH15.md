# Data Modelling and Databases - Chapter 15 (Book)

- Author: Ruben Schenk
- Date: 24.05.2021
- Contact: ruben.schenk@inf.ethz.ch

# 15. Query Execution

The `query processor` is the group of components of a DBMS that turns user queries and data-modification commands into a sequence of database operations and executes those operations.

#### Preview of Query Compilation

To set the context for query execution, we offer a very brief outline of the content of the next chapter:

1. `Parsing`. A `parse tree` for the query is constructed.
2. `Query Rewrite`. The parse tree is converted to an initial query plan, which is usually an algebraic representation of the query.
3. `Physical Plan Generation`. The abstract query plan from (2), often called a `logical query plan`, is turned into a `physical query plan` by selecting algorithms to implement each of the operators of the logical plan, and by selecting an order of execution for these operators.

Parts (2) and (3) are often called the `query optimizer`, and these are the hard parts of the query compilation.

<img src="./Figures/DMDB_BN_Fig15-2.PNG" height="550px"/><br>

*Figure 15.2: Outline of query compilation.*

## 15.1 Introduction to Physical-Query-Plan Operators

### 15.1.1 Scanning Tables

Perhaps the most basic thing we can do in a physical query plan is to read the entire contents of a relation $R$. There are two basic approaches to locating the tuples of a relation $R$:

1. In many cases, the relation $R$ is stored in an area of secondary memory, with its tuples arranged in blocks. The blocks containing the tuples of $R$ are known to the system, and it is possible to get the blocks one by one. This operation is called `table-scan`.
2. If there is an index on any attribute of $R$, we may be able to use this index to get all the tuples of $R$. This operation is called `index-scan`.

### 15.1.2 Sorting While Scanning Tables

The physical-query-plan operator `sort-scan` takes a relation $R$ and a specification of the attributes on which the sort is to be made, and produces $R$ in that sorted order. There are several ways that sort-scan can be implemented, for example if there is a B-tree index on the attribute to be sorted by.

### 15.1.3 The Computation Model for Physical Operators

We shall use the number of disk I/O's as our measure of cost for an operation. When comparing algorithms for the same operations, we shall make an assumption that may be surprising at first:

- We assume that the arguments of any operator are found on disk, but the result of the operator is left in main memory.

### 15.1.4 Parameters for Measuring Costs

Now, let us introduce the parameters (sometimes called statistics) that we use to express the cost of an operator.

$M$ will denote the number of main-memory buffers available to an execution of a particular operator.  
We shall furthermore make the simplifying assumption that data is accessed one block at a time from disk.  
There are three parameter families, $B$, $T$, and $V$:

- When describing the size of a relation $R$, we most often are concerned with the number of blocks that are needed to hold all the tuples of $R$. This number of blocks will be denoted $B(R)$. Usually, we assume that $R$ is `clustered`, that is, it is stored in $B$ blocks or in approximately $B$ blocks.
- Sometimes, we also need to know the number of tuples in $R$, and we denote this quantity by $T(R)$.
- Finally, we shall sometimes want to refer to the number of distinct values that appear in a column of a relation. If $R$ is a relation, and one of its attributes is $a$, then $V(R, \, a)$ is the number of distinct values of the column for $a$ in $R$.

### 15.1.5 I/O Cost for Scan Operators

IF relation $R$ is clustered, then the number of disk I/O's for the table-scan operator is approximately $B$. Likewise, if $R$ fits in main-memory, we can implement sort-scan by reading $R$ into memory and performing an in-memory sort, again requiring only $B$ disk I/O's.

However, if $R$ is distributed among tuples of other relations, then a table-scan for $R$ may require reading as many blocks as there are tuples of $R$, that is, the I/O cost is $T$.

### 15.1.6 Iterators for Implementation of Physical Operators

Many physical operators can be implemented as an `iterator`, which is a group of three methods that allows a consumer of the result of the physical operator to get the result one tuple at a time. The three methods forming the iterator for an operation are:

1. `Open()`. This method starts the process of getting tuples, but does not get a tuple.
2. `GetNext()`. This method returns the next tuple in the result and adjusts data structures as necessary to allow subsequent tuples to be obtained.
3. `Close()`. This method ends the iteration after all tuples, or all tuples that the consumer wanted, have been obtained.

Example: Perhaps the simplest iterator is the one that implements the table-scan operator. Fig. 15.3 sketches the three methods for this iterator.

```sql
    Open() {
        b := the first block of R;
        t := the first tuple of block b;
    }

    GetNext() {
        IF (t is past the last tuple on block b) {
            increment b to the next block;
            IF (there is no next block) {
                RETURN NotFound;
            } ELSE /* b is a new block */ {
                t := first tuple on block b;
            }
        } /* now we are ready to return t and increment */
        oldt := t;
        increment t to the next tuple of b;
        RETURN oldt;
    }

    CLOSE() {

    }
```

*Figure 15.3: Iterator methods for the table-scan operator over relation R.*

Example: Let us consider another example of how iterators can be combined by calling other iterators. The operation is the bag union $R \cup S$, in which we produce first all the tuples of $R$ and then all the tuples of $S$, without regard for the existence of duplicates. The iterator methods for the union are sketched in Fig. 15.4.

```sql
    Open() {
        R.Open();
        CurRel := R;
    }

    GetNext() {
        IF (CurRel = R) {
            t := R.GetNext();
            IF (t <> NotFound) /* R is not exhausted */ {
                RETURN T;
            } ELSE /* R is exhausted */ {
                S.Open;
                CurRel = S;
            }
        }
        /* here, we must read from S */
        RETURN S.GetNext();
        /* notice that if S is exhausted, S.GetNext()
        will return NotFound, which is the correct
        action for our GetNext as well */
    }

    Close() {
        R.Close();
        S.Close();
    }
```

*Figure 15.4: Building a union iterator from iterators $\mathcal{R}$ and $\mathcal{S}$.*
