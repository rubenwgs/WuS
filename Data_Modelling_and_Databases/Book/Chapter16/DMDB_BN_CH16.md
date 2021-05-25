# Data Modelling and Databases - Chapter 16 (Book)

- Author: Ruben Schenk
- Date: 25.05.2021
- Contact: ruben.schenk@inf.ethz.ch

# 16. The Query Compiler

We shall now take up the architecture of the query compiler and its optimizer. There are three broad steps that the query processor must take:

1. The query, written in a language like SQL, is `parsed`, that is, turned into a parse tree representing the structure of the query in a useful way.
2. The parse tree is transformed into an expression tree of relational algebra, which we term a `logical query plan`.
3. The logical query plan must be turned into a `physical query plan`, which indicates not only the operations performed, but the order in which they are performed, the algorithm used to perform each step, etc.

## 16.1 Parsing and Preprocessing

The first stages of query compilation are illustrated in Fig. 16.1:

<img src="./Figures/DMDB_BN_Fig16-1.PNG" height="450px"/><br>

### 16.1.1 Syntax Analysis and Parse Trees

Tje job of the parser is to take text written in a language such as SQL and convert it to a `parse tree`, which is a tree whose nodes correspond to either:

1. `Atoms`, which are lexical elements such as keywords (e.g. *SELECT*), names of attributes or relations, constants, parentheses, operators such as $+$ or $<$, and other schema elements, or
2. `Syntactic categories`, which are names for families of query subparts. WE shall represent syntactic categories by triangular brackets around a name. For example, $<\text{Query}>$ will be used to represent some queries in the common select-from-where form, and $<\text{Condition}>$ will represent any expression that is a condition.

If a node is an atom, then it has no children. However, if the node is a syntactic category, then its children are described by one of the `rules` of the grammar for the language.

### 16.1.2 A Grammar for a Simple Subset of SQL

We shall illustrate the parsing process by giving some rules that describe a small subset of SQL queries.

#### Queries

The syntactic category $<\text{Query}>$ is intended to represent some of the queries of SQL. We give it only one rule:

```sql
 <Query> ::= SELECT <SelList> FROM <FromList> WHERE <Condition>
```

Symbol $::=$ means "*can be expressed as*"

Note this rule does not provide for the various optional clauses such as *GROUP BY, HAVING,* or *ORDER BY*, nor for options such as *DISTINCT* after *SELECT*, nor for query expressions using *UNION, JOIN,* or other binary operators.

#### Select-Lists

```sql
    <SelList> ::= <Attribute>, <SelList>
    <SelList> ::= <Attribute>
```

These two rules say that a select-list can be any comma-separated list of attributes. Either a single attribute or an attribute, a comma, and any list of on ore more attributes.

#### From-Lists

```sql
    <FromList> ::= <Relation>, <FromList>
    <FromList> ::= <Relation>
```

Here, a from-list is defined to be any comma-separated list of relations.

#### Conditions

The rules we shall use are:

```sql
    <Condition> ::= <Condition> AND <Condition>
    <Condition> ::= <Attribute> IN ( <Query> )
    <Condition> ::= <Attribute> = <Attribute>
    <Condition> ::= <Attribute> LIKE <Pattern>
```

Although we have listed more rules for conditions than for other categories, these rules only scratch the surface of the forms of conditions.

#### Base Syntactic Categories

Syntactic categories $<\text{Attribute}>$, $<\text{Relation}>$, and $<\text{Pattern}>$ are special, in that they are not defined by grammatical rules, but by rules about the atoms for which they can stand:

- $<\text{Attribute}>$ can be any string of characters that identifies an attribute of the current database schema.
- $<\text{Relation}>$ can be replaced by any string of characters that makes sense as a relation in the current schema.
- $<\text{Pattern}>$ can be replaced by any quoted string that is a legal SQL patter.

Example: REcall two relations from the running movies example:

```sql
    StarsIn(movieTitle, movieYear, starName)
    MovieStar(name, address, gender, birthdate)
```

Our study of parsing and query rewriting will center around the query "*find the titles of movies that have at least one star born in 1960*".  
The SQL for this variation of the query is shown in Fig. 16.2:

```sql
    SELECT movieTitle
    FROM StarsIn
    WHERE starName IN (
        SELECT name
        FROM MovieStar
        WHERE birthdate LIKE '%1960'
    )
```

*Figure 16.2: Find the movies with stars born in 1960.*

The parse tree for the query of Fig. 16.2, according to the grammar we have sketched, is shown in Fig. 16.3:

<img src="./Figures/DMDB_BN_Fig16-3.PNG" width="550px"/><br>

*Figure 16.3: The parse tree for Fig. 16.2.*

### 16.1.3 The Processor

The `preprocessor` has several important functions. If a relation used in the query is actually a virtual viw, then each use of this relation in the form-list must be replaced by a parse tree that describes the view.

The preprocessor is also responsible for `semantic checking`. Even if the query is valid syntactically, it actually may violate one or more semantic rules on the use of names. For instance, the preprocessor must:

1. `Check relation uses`: Every relation mentioned in a FROM-clause must be a relation or view in the current schema.
2. `Check and resolve attribute uses`: Every attribute that is mentioned in the SELECT- or WHERE-clause must be an attribute of some relation in the current scope.
3. `Check types`: All attributes must be of a type appropriate to their uses.

### 16.1.4 Preprocessing Queries Involving Views

When an operand in a query is a virtual view, the preprocessor needs to replace the operand by a piece of parse tree that represents how the view is constructed from base tables.

To form the query over base tables, we substitute, for each leaf in the tree for $Q$ that is a view, the root of a copy of the three that defines that view.

Example: Let us consider the view definition of a previous example. Recall the definition of view *ParamountMovies* is:

```sql
    CREATE VIEW ParamountMovies AS
        SELECT title, year
        FROM Movies
        WHERE studioName = 'Paramount';
```

The tree in Fig. 16.7 is relational-algebra expression for the query:

<img src="./Figures/DMDB_BN_Fig16-7.PNG" height="350px"/><br>

*Figure 16.7: Expression tree for view ParamountMovies.*

The query of our previous example was:

```sql
    SELECT title
    FROM ParamountMovies
    WHERE year = 1979;
```

This query has the expression tree shown in Fig. 16.8 below:

<img src="./Figures/DMDB_BN_Fig16-8.PNG" height="350px"/><br>

*Figure 16.8: Expression tree for the query.*

We substitute the tree of Fig. 16.7 for the leaf *ParamountMovies* in Fig 16.8. The resulting thee is shown in Fig 16.9 below:

<img src="./Figures/DMDB_BN_Fig16-9.PNG" height="450px"/><br>

*Figure 16.9: Expressing the query in terms of base tables.*

## 16.2 Algebraic Laws for Improving Query Plans

### 16.2.1 Commutative and Associative Laws

A `commutative law` about an operator says that it does not matter in which order you present the arguments of the operator, the result will be the same. For instance, $+$ and $\times$ are commutative operators of arithmetic. More precisely, $x+y = y+x$ and $x \times y = y \times x$ for any number $x$ and $y$.

An `associative law` about an operator says that we may group two uses of the operator either from the left or the right. For instance $+$ and $\times$ are associative arithmetic operations, meaning that $(x + y) + z = (x + (y + z))$ and $(x \times y) \times z = x \times (y \times z)$.

Several operators of relational algebra are both associative and commutative. Particularly:

- $R \times S = S \times R$ and $(R \times S) \times T = R \times (S \times T)$
- $R \Join S = S \Join R$ and $(R \Join S) \Join T = R \Join (S \Join T)$
- $R \cup S = S \cup R$ and $(R \cup S) \cup T = R \cup (S \cup T)$
- $R \cap S = S \cap R$ and $(R \cap S) \cap T = R \cap (S \cap T)$

Note that these laws hold for both sets and bags.

### 16.2.2 Laws Involving Selection

Since selections tend to reduce the size of relations markedly, one of the most important rules of efficient query processing is to move the selections down the tree as far as they will go without changing what the expression does.

Our first two laws for $\sigma$ are the `splitting laws`:

- $\sigma_{C_1 \text{ AND } C_2} (R) = \sigma_{C_1}(\sigma_{C_2}(R))$
- $\sigma_{C_1 \text{ OR } C_2} = (\sigma_{C_1}(R)) \cup_S (\sigma_{C_2}(R))$

However, the second law, for $\text{OR}$, only work if the relation $R$ is a set.  
Notice that the order of $C_1$ and $C_2$ is flexible. WE can swap the order of any sequence of $\sigma$ operators:

- $\sigma_{C_1}(\sigma_{C_2}(R)) = \sigma_{C_2}(\sigma_{C_1}(R))$

The next family of laws involving $\sigma$ allows us to push selections through the binary operators: product, union, intersection, difference, and join. There are three types of laws:

1. For union, the selection must be pushed to both arguments.
2. For a difference, the selection must be pushed to the first argument and optionally may be pushed to the second.
3. For the other operators it is only required that the selection be pushed to one argument.

Thus, the law for union is:

- $\sigma_{C}(R \cup S) = \sigma_{C}(R) \cup \sigma_C (S)$

For difference, one version of the law is:

- $\sigma_C(R - S) = \sigma_C(R) - S$
- $\sigma_C(R - S) = \sigma_C(R) - \sigma_C(S)$

The next laws allow the selection to be pushed to one or both arguments. We shall show the laws below assuming that the relation $R$ has all the attributes mentioned in $C$:

- $\sigma_C(R \times S) = \sigma_C(R) \times S$
- $\sigma_C(R \Join S) = \sigma_C(R) \Join S$
- $\sigma_C (R \Join_D S) = \sigma_C(R) \Join_D S$
- $\sigma_C(R \cap S) = \sigma_C(R) \cap S$

If $C$ has only attributes of $S$, then we can instead write:

- $\sigma_C(R \times S) = R \times \sigma_C(S)$

Should relations $R$ and $S$ both happen to have all attributes of $C$, then we can use laws such as:

- $\sigma_C(R \Join S) = \sigma_C(R) \Join \sigma_C(S)$

### 16.2.3 Pushing Selections

As we illustrated, pushing a selection down an expression tree is oen of the most powerful tools of the query optimizer. However, when queries involve virtual views, it is sometimes necessary to first move a selection as far up the tree as it can go, and then push the selection down all possible branches.

### 16.2.4 Laws Involving Projection

Projections, like selections, can be pushed down through many other operators. Pushing projections differs from pushing selections in that when we push projections, it is quite usual for the projection also to remain where it is.

To describe the transformations of extended projection, we need to introduce some terminology. Consider e term $R \rightarrow x$ on the list for a projection, where $E$ is an attribute or an expression involving attributes and constants. We say all attributes mentioned in $E$ are `input attributes` of the projection, and $x$ is an `output attribute`.  
If a projection list consists only of attributes, with no renaming or expressions other than a single attribute, then we say the projection is `simple`.

The principle behind laws for projection is that:

- We may introduce a projection anywhere in an expression tree, as long as it eliminates only attributes that are neither used by an operator above nor are in the result of the entire expression.

We introduce the following laws:

- $\pi_L(R \Join S) = \pi_L(\pi_M(R) \Join \pi_N(S))$, where $M$ and $N$ are the join attributes and the input attributes in $L$ that are found among the attributes of $R$ and $S$, respectively.
- $\pi_L(R \Join_C S) = \pi_L(\pi_M(R) \Join_C \pi_N(S))$, where $M$ and $N$ are the join attributes and the input attributes of $R$ and $S$, respectively.
- $\pi_L(R \times S) = \pi_L(\pi_M(R) \times \pi_N(S))$, where $M$ and $N$ are the lists of all attributes of $R$ and $S$, respectively, that are input attributes of $L$.

We can perform a projection entirely before a bag union. That is:

- $\pi_L(R \cup_B S) = \pi_L(R) \cup_B \pi_L (S)$

It is also possible to push a projection below a selection:

- $\pi_L(\sigma_C(R)) = \pi_L(\sigma_C(\pi_M(R)))$, where $M$ is the list of attributes that are either input attributes of $L$ or mentioned in condition $C$.

### 16.2.5 Laws About Joins and Products

There are a few additional laws that follow directly from the definition of the join:

- $R \Join_C S = \sigma_C(R \times S)$
- $R \Join S = \pi_L(\sigma_C(R \times S))$, where $C$ is the condition that equates each pair of attributes from $R$ and $S$ with the same name, and $L$ is a list that includes one attribute from each equated pair and all other attributes of $R$ and $S$.

### 16.2.6 Laws Involving Duplicate Elimination

The operator $\delta$, which eliminates duplicates from a bag, can be pushed thorough many, but not all operators.

- $\delta(R) = R$ if $R$ has no duplicates.

Several laws that push $\delta$ through other operators are:

- $\delta(R \times S) = \delta(R) \times \delta (S)$
- $\delta(R \Join S) = \delta(R) \Join \delta(S)$
- $\delta(R \Join_C S) = \delta(R) \Join_C \delta(S)$
- $\delta(\sigma_C(R)) = \sigma_C(\delta(R))$

We can also move the $\delta$ to either or both of the arguments of an intersection:

- $\delta(R \cap_B S) = \delta(R) \cap_B S = R \cap_B \delta(S) = \delta(R) \cap_B \delta(S)$

On the other hand, $\delta$ generally cannot be pushed through the operators $\cup_B$, $-_B$, or $\pi$.

### 16.2.7 Laws Involving Grouping and Aggregation

When we consider the operator $\gamma$, we cannot state laws in the generality that we used for the other operators. One exception is the law, that $\gamma$ absorbs a $\delta$. Precisely:

- $\delta(\gamma_L(R)) = \gamma_L(R)$

Another general rule is:

- $\gamma_L(R) = \gamma_L(\pi_M(R))$ if $M$ is a list containing at least all those attributes of $R$ that are mentioned in $L$.

Furthermore, let us call an operator $\gamma_L$ `duplicate-impervious` if the only aggregations in $L$ are $\text{MIN}$ and/or $\text{MAX}$. Then:

- $\gamma_L(R) = \gamma_L(\delta(R))$ provided $\gamma_L$ is duplicate-impervious.
