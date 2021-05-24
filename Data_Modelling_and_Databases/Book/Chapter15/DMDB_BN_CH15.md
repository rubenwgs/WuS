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