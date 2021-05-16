# Data Modelling and Databases - Chapter 3 (Book)

- Author: Ruben Schenk
- Date: 13.05.2021
- Contact: ruben.schenk@inf.ethz.ch

# 3. Design Theory for Relational Databases

## 3.1 Functional Dependencies

There is a design theory for relations that lets us examine a design carefully and make improvements based on a few simple principles. The theory begins by having us state the constraints that apply to the relation. The most common constraint is the "functional dependency", a statement of a type that generalizes the idea of a key for a relation.

Later in this chapter, we shall see how this theory gives us simple tools to improve our designs by the process of "decomposition" of relations: the replacement of one relation by several, whose sets of attributes together include all the attributes of the original.

### 3.1.1 Definition of Functional Dependency

A `functional dependency (FD)` on a relation $R$ is a statement of the form:

> *If two tuples of $R$ agree on all of the attributes $A_1, \, A_2,..., \, A_n$, then they must also agree on all of another list of attributes $B_1, \, B_2,..., \, B_m$.*

We write this FD formally as $A_1 A_2 \cdots A_n \rightarrow B_1 B_2 \cdots B_m$ and say that

$$
"A_1, \, A_2,..., \, A_n \text{ functionally determine } B_1, \, B_2,..., \, B_m"
$$

<img src="./Figures/DMDB_Fig3-1.PNG" width="400px"/><br>

If we can be sure every instance of a relation $R$ will be one in which a given FD is true, then we say that $R$ `satisfies` the FD. It is common for the right side of an FD to be a single attribute.

Example: Let us consider the relation *Movies1(title, year, length, genre, studioName, starName)*. To see what is wrong with the design, we first determine the functional dependencies that hold for the relation. We claim that the following FD folds:

$$
\text{title year } \rightarrow \text{ length genre studioName}
$$

Informally, this FD says that if two tuples have the same value in their *title* and *year* components, then these tuples must also have the same values in their *length*, *genre*, and *studioName* components.

On the other hand, we observe that the statement

$$
\text{title year } \rightarrow \text{ starName}
$$

is false. It is not a functional dependency. Given a movie, it is entirely possible that there is more than one star for the movie listed in our database.

### 3.1.2 Key of Relations

We say a set of one or more attributes $\{A_1, \, A_2,..., , A_n \}$ is a `key` for a relation $R$ if:

1. Those attributes functionally determine all other attributes of the relation. That is, it is impossible for two distinct tuples of $R$ to agree on all of $A_1, \, A_2,..., \, A_n$.
2. No proper subset of $\{A_1, \, A_2,..., \, A_n \}$ functionally determines all other attributes of $R$, i.e., a key must be `minimal`.

When a key consists of a single attribute $A$, we often say that $A$ (rather than $\{A \}$) is a key.

### 3.1.3 Superkeys

A set of attributes that contains a key is called a `superkey`, short for "superset of a key". Thus, every key is a superkey. However, some superkeys are not (minimal) keys. Note that every superkey satisfies the first condition of a key: it functionally determines all other attributes of the relation. However, a superkey need not satisfy the second condition: minimality.

Example: In our *Movies1* relation, there are many superkeys. Not only is the key

$$
\{\text{title, year, starName} \}
$$

a superkey, but any superset of this set of attributes, such as

$$
\{\text{title, year, starName, length, studioName}\}
$$

is a superkey.

## 3.2 Rules About Functional Dependencies

In this section, we shall learn how to `reason` about FD's. That is, suppose we are told of a set of FD's that a relation satisfies. OFten, we can deduce that the relation must satisfy certain other FD's. This ability to discover additional FD's is essential when we discuss the design of good relation schemas in Section 3.3.

### 3.2.1 Reasoning About Functional Dependencies

Let us begin with a motivating example that will show us how we can infer a functional dependency from other given FD's.

Example: If we are told that a relation $R(A, \, B, \, C)$ satisfies the FD's $A \rightarrow B$ and $B \rightarrow C$, then we can deduce that $R$ also satisfies the FD $A \rightarrow C$.

To prove that $A \rightarrow C$, we must consider two tuples of $R$ that agree on $A$ and prove they also agree on $C$.

Let the tuples agreeing on attribute $A$ be $(a, \, b_1, \, c_1)$ and $(a, \, b_2, \, c_2)$. Since $R$ satisfies $A \rightarrow B$, and these tuples agree on $A$, they must also agree on $B$. That is, $b_1 = b_2 = b$, and the tuples are really $(a, \, b, \, c_1)$ and $(a, \, b, \, c_2)$. Similarly, since $R$ satisfies $B \rightarrow C$, and the tuples agree on $B$, they agree on $C$. Thus, $c_1 = c_2 = c$, i.e., the tuples do agree on $C$, and that is the FD $A \rightarrow C$ we wanted to prove.

FD's can often be presented in several different ways, without changing the set of legal instances of the relation. We say:

- Two sets of FD's $S$ and $T$ are `equivalent` if the set of relation instances satisfying $S$ is exactly the same as the set of relation instances satisfying $T$.
- More generally, a set of FD's $S$ `follows` from a set of FD's $T$ if every relation instance that satisfies all the FD's in $T$ also satisfies all the FD's in $S$.

Note that two sets of FD's $S$ and $T$ are equivalent if and only if $S$ follows from $T$, and $T$ follows from $S$.

### 3.2.2 The Splitting/Combining Rule

Recall that we commented that the FD:

$$
A_1 A_2 \cdots A_n \rightarrow B_1 B_2 \cdots B_m
$$

is equivalent to the set of FD's:

$$
A_1 A_2 \cdots A_n \rightarrow B_1, \quad A_1 A_2 \cdots A_n \rightarrow B_2, \quad ... \quad A_1 A_2 \cdots A_n \rightarrow B_m
$$

That is, we may split attributes on the right side so that only one attribute appears on the right of each FD. Likewise, we can replace a collection of FD's having a common left side by a single FD with the same left side and all the right sides combined into one set of attributes.

- We can replace FD $A_1 A_2 \cdots A_n \rightarrow B_1 B_2 \cdots B_m$ by a set of FD's $A_1 A_2 \cdots A_n \rightarrow B_i$ for $i = 1, \, 2,..., \, m$. We call this transformation the `splitting rule`.
- We can replace a set of FD's $A_1 A_2 \cdots A_n \rightarrow B_i$ for $i = 1, \, 2,..., \, m$ by the single FD $A_1 A_2 \cdots A_n \rightarrow B_1 B_2 \cdots B_m$. We call this transformation the `combining rule`.

Example: In a previous example, the set of FD's:

$$
\text{title year } \rightarrow \text{ length} \\
\text{title year } \rightarrow \text{ genre} \\
\text{title year } \rightarrow \text{ studioName} \\
$$

is equivalent to the single FD:

$$
\text{title year } \rightarrow \text{ length genre studioName} \\
$$

that we asserted there.

### 3.2.3 Trivial Functional Dependencies

A constraint of any kind on a relation is said to be `trivial` if it holds for every instance of the relation, regardless of what other constraints are assume. When the constraints are FD's, it is easy to tell whether an FD is trivial. They are the FD's $A_1 A_2 \cdots A_n \rightarrow B_1 B_2 \cdots B_m$ such that

$$
\{B_1, \, B_2, ..., \, B_m \} \subseteq \{A_1, \, A_2,..., \, A_n \}
$$

That is, a trivial FD has a right side that is a subset of its left side. For example,

$$
\text{title year } \rightarrow \text{ title}
$$

is a trivial FD, as is

$$
\text{title } \rightarrow \text{ title}
$$

There is an intermediate situation in which some, but not all, of the attributes on the right side of an FD are also on the left. This FD is not trivial, but it can be simplified by removing from the right side of an FD those attributes that appear on the left. That is:

- The FD $A_1 A_2 \cdots A_n \rightarrow B_1 B_2 \cdots B_m$ is equivalent to

    $$
    A_1 A_2 \cdots A_n \rightarrow C_1 C_2 \cdots C_k
    $$

    where the $C$'s are all those $B$'s that are not also $A$'s.

We call this rule, illustrated in the figure below, the `trivial-dependency rule`.

<img src="./Figures/DMDB_Fig3-2.PNG" width="400px"/><br>

### 3.2.4 Computing the Closure of Attributes

Before proceeding to other rules, we shall give a general principle from which all true rules follow. Suppose $\{A_1, \, A_2,..., \, A_n \}$ is a set of attributes and $S$ is a set of FD's.

The `closure` of $\{A_1, \, A_2,..., \, A_n \}$ under the FD's in $S$ is the set of attributes $B$ such that every relation that satisfies all the FD's in set $S$ also satisfies $A_1 A_2 \cdots A_n \rightarrow B$. We denote the closure of a set of attributes $A_1 A_2 \cdots A_n$ by $\{A_1, \, A_2,..., \, A_n \}^+$. Note that $A_1, \, A_2,..., \, A_n$ are always in $\{A_1, \, A_2,..., \, A_n \}^+$ because the FD $A_1 A_2 \cdots A_n \rightarrow A_i$ is trivial when $i$ is one of $1, \, 2,..., \, n.$

#### Algorithm 3.7: Closure of a Set of Attributes

**INPUT:** A set of attributes $\{A_1, \, A_2,..., \, A_n \}$ and a set of FD's $S$.

**OUTPUT:** The closure $\{A_1, \, A_2,..., \, A_n \}^+.$

1. If necessary, split the FD's of $S$, so each FD in $S$ has a single attribute on the right.
2. Let $X$ be a set of attributes that eventually will become the closure. Initialize $X$ to be $\{A_1, \, A_2,..., \, A_n \}$.
3. Repeatedly search for some FD

    $$
    B_1 B_2 \cdots B_m \rightarrow C
    $$

    such that all of $B_1, \, B_2,..., \, B_m$ are in the set of attributes $X$, but $C$ is not. Add $C$ to the set of $X$ and repeat the search. Since $X$ can only grow, and the number of attributes of any relation schema must be finite, eventually nothing more can be added to $X$, and this step ends.
4. The set $X$, after no more attributes can be added to it, is the correct value of $\{A_1, \, A_2,..., \, A_n \}^+$.

By computing the closure of any set of attributes, we can test whether any given FD $A_1 A_2 \cdots A_n \rightarrow B$ follows from a set of FD's $S$. More generally, $A_1 A_2 \cdots A_n \rightarrow B_1 B_2 \cdots B_m$ follows from set of FD's $S$ if and only if all of the $B_1, \, B_2,..., \, B_m$ are in

$$
\{A_1, \, A_2,..., \, A_n \}^+
$$

### 3.2.5 Why the Closure Algorithm Works

In this section, we shall show why Algorithm 3.7 correctly decides whether or not an FD $A_1 A_2 \cdots A_n \rightarrow B$ follows from a given set of FD's $S$. There are two parts to the proof:

1. We must show that Algorithm 3.7 does not claim too much. That is, we must show that if $A_1 A_2 \cdots A_n \rightarrow B$ is asserted by the closure test, then $A_1 A_2 \cdots A_n \rightarrow B$ holds in any relation that satisfies all the FD's in $S$.
2. We must prove that Algorithm 3.7 does not fail to discover a FD that truly follows from the set of FD's $S$.

#### Why the Closure Algorithm Claims only True FD's

We can prove by induction on the number of times that we apply the growing operation of Step 3 that for every attribute $D$ in $X$, the FD $A_1 A_2 \cdots A_n \rightarrow D$ holds.

**Basis:** The basic case is when there are zero steps. Then $D$ must be one of $A_1, \, A_2,..., \, A_n$, and surely $A_1 A_2 \cdots A_n \rightarrow D$ holds in any relation, because it is a trivial FD.

**Induction:** For the induction, suppose $D$ was added when we used the FD $B_1 B_2 \cdots B_m \rightarrow D$ of $S$. We know by the inductive hypothesis is that $R$ satisfies $A_1 A_2 \cdots A_n \rightarrow B_1 B_2 \cdots B_m$. Now, suppose two tuples of $R$ agree on all of $A_1, \, A_2,..., \, A_n$. Then since $R$ satisfies $A_1 A_2 \cdots A_n \rightarrow B_1 B_2 \cdots B_m$, the two tuples must agree on all of $B_1, \, B_2,..., \, B_m$. Since $R$ satisfies $B_1 B_2 \cdots B_m \rightarrow D$, we also know these two tuples agree on $D$. Thus, $R$ satisfies $A_1 A_2 \cdots A_n \rightarrow D$.

#### Why the Closure Algorithm Discovers All True FD's

Suppose $A_1 A_2 \cdots A_n \rightarrow B$ were an FD Algorithm 3.7 says does not follow from set $S$. WE must show that FD $A_1 A_2 \cdots A_n \rightarrow B$ really doesn't follow from $S$. That is, we must show that there is at least one relation instance that satisfies all the FD's in $S$, and yet does not satisfy $A_1 A_2 \cdots A_n \rightarrow B$.

This instance $I$ is actually quite simple to construct. It is shown in the table below. $I$ has only two tuples: $t$ and $s$. The two tuples agree in all the attributes of $\{A_1, \, A_2,..., , A_n \}^+$, and they disagree in all other attributes. We must show first that $I$ satisfies all the FD's of $S$. and then that it does not satisfy $A_1 A_2 \cdots A_n \rightarrow B$.

|      | $\{A_1, \, A_2,..., , A_n \}^+$ | Other Attributes |
| :--- | :-----------------------------: | :--------------: |
| $t:$ | $111 \cdots 11$                 | $000 \cdots 00$  |
| $s:$ | $111 \cdots 11$                 | $111 \cdots 11$  |

Suppose there were some FD $C_1 C_2 \cdots C_k \rightarrow D$ in set $S$ that instance $I$ does not satisfy. Since $I$ has only two tuples, $t$ and $s$, those must be the two tuples that violate $C_1 C_2 \cdots C_k \rightarrow D$. That is, $t$ and $s$ agree in all the attributes of $\{C_1, \, C_2,..., \, C_k \}$, yet disagree on $D$. If we examine the table above, we see that all of $C_1, \, C_2,..., , C_k$ must be among the attributes of $\{A_1, \, A_2,..., \, A_n \}$, because those are the only attributes on which $t$ and $s$ agree. Likewise, $D$ must be among the other attributes, because only on those attributes do $t$ and $s$ disagree.

But then we did not compute the closure correctly. $C_1 C_2 \cdots C_k \rightarrow D$ should have been applied when $X$ was $\{A_1, \, A_2, ..., \, A_n\}$ to add $D$ to $X$. We conclude that $C_1 C_2 \cdots C_k \rightarrow D$ cannot exist, i.e. instance $I$ satisfies $S$.

Second, we must show that $I$ does not satisfy $A_1 A_2 \cdots A_n \rightarrow B$. However, this part is easy. Surely, we know that $B$ is not in $\{A_1, \, A_2,.., \, A_n \}^+$, so $B$ is one of the attributes on which $t$ and $s$ disagree. Thus, $I$ does not satisfy $A_1 A_2 \cdots A_n \rightarrow B$.

### 3.2.6 The Transitive Rule

The `transitive rule` lets us cascade two FD's, and generalizes the observation of a previous example:

- If $A_1 A_2 \cdots A_n \rightarrow B_1 B_2 \cdots B_m$ and $B_1 B_2 \cdots B_m \rightarrow C_1 C_2 \cdots C_k$ hold in relation $R$, then $A_1 A_2 \cdots A_n \rightarrow C_1 C_2 \cdots C_k$ also holds in $R$.

If some of the $C$'s are among the $A$'s, we may eliminate them from the right side by the trivial-dependencies rule.

Example: The following table shows another version of the *Movies* relation:

| $title$       | $year$ | $length$ | $genre$ | $studioName$ | $studioAddr$ |
| :------------ | :----- | :------- | :------ | :----------- | :----------- |
| Star Wars     | 1977   | 124      | sciFi   | Fox          | Hollywood    |
| Eight Below   | 2005   | 120      | drama   | Disney       | Buena Vista  |
| Wayne's World | 1992   | 95       | comedy  | Paramount    | Hollywood    |

Two of the FD's that we might reasonably claim to hold are:

$$
\text{title year } \rightarrow \text{ studioName} \\
\text{studioName } \rightarrow \text{ studioAddr}
$$

The transitive rule allows us to combine the two FD's above to get a new FD:

$$
\text{title year } \rightarrow \text{ studioAddr}
$$

### 3.2.7 Closing Sets of Functional Dependencies

Sometimes we have a choice of which FD's we use to represent the full set of FD's for a relation. If we are given a set of FD's $S$, then any set of FD's equivalent to $S$ is said to be a `basis` for $S$. To avoid some of the explosion of possible bases, we shall limit ourselves to considering only bases whose FD's have singleton right sides. If we have any basis, we can apply the splitting rule to make the right sides be singletons. A `minimal basis` for a relation is a basis $B$ that satisfies three conditions:

1. All the FD's in $B$ have singleton right sides.
2. If any FD is removed from $B$, the result is no longer a basis.
3. If for any FD in $B$ we remove one or more attributes from the left side of $F$, the result is no longer a basis.

### 3.2.8 Projecting Functional Dependencies

Suppose we have a relation $R$ with set of FD's $S$, and we project $R$ by computing $R_1 = \pi_L (R)$, for some list of attributes $R$. What FD's hold in $R_1$?

The answer is obtained in principle by computing the `projection of functional dependencies` $S$, which is all FD's that:

1. Follow from $S$, and
2. Involve only attributes of $R_1$

Since there may be a larger number of such FD's, and many of them may be redundant, we are free to simplify that set of FD's if we wish. The simple algorithm is summarized below:

#### Algorithm 3.12: Projecting a Set of Functional Dependencies

**INPUT:** A relation $R$ and a second relation $R_1$ computed by the projection $R_1 = \pi_L (R)$. Also, a set of FD's $S$ that hold in $R$.

**OUTPUT:** The set of FD's that hold in $R_1$.

**METHOD:**

1. Let $T$ be the eventual output set of FD's. Initially, $T$ is empty.
2. For each set of attributes $X$ that is a subset of the attributes of $R_1$, compute $X^+$. This computation is performed with respect to the set of FD's $S$, and may involve attributes that are in the schema of $R$ but not in $R_1$. Add to $T$ all nontrivial FD's $X \rightarrow A$ such that $A$ is both in $X^+$ and an attribute of $R_1$.
3. Now, $T$ is a basis for the FD's that hold in $R_1$, but may not be a minimal basis. We may construct a minimal basis by modifying $T$ as follows:
   1. If there is an FD $F$ in $T$ that follows from the other FD's in $T$, remove $F$ from $T$.
   2. Let $Y \rightarrow B$ be an FD in $T$, with at least two attributes in $Y$, and let $Z$ be $Y$ with one of its attributes removed. If $Z \rightarrow B$ follows from the FD's in $T$ (including $Y \rightarrow B$), then replace $Y \rightarrow B$ by $Z \rightarrow B$.
   3. Repeat the above steps in all possible way until no more changes to $T$ can be made.

## 3.3 Design of Relational Database Schemas

Careless selection of relational database schema can lead to redundancy and related anomalies. In this section, we shall tackle the problem of design of good relation schemas in the following stages:

1. We first explore in more details the problems that arise when our schema is poorly designed.
2. Then, we introduce the idea of "decomposition", breaking a relation schema into two smaller schemas.
3. Next, we introduce "Boyce-Codd normal form", or "BCNF", a condition on a relation schema that eliminates these problems.
4. These points are tied together when we explain how to assure the BCNF condition by decomposing relation schemas.

### 3.3.1 Anomalies

Problems such as redundancy that occur when we try to cram too much into a single relation are called `anomalies`. The principal kind of anomalies that we encounter are:

1. `Redundancy`. Information may be repeated unnecessarily in several tuples.
2. `Update Anomalies`. We may change information in one tuple but leave the same information unchanged in another.
3. `Deletion Anomalies`. If a set of values becomes empty, we may lose other information as a side effect.

### 3.3.2 Decomposing Relations

The accepted way to eliminate these anomalies is to `decompose` relations. Decomposition of $R$ involves splitting the attributes of $R$ to make the schemas of two new relations.

Given a relation $R(A_1, \, A_2, ..., \, A_n)$, we may `decompose` $R$ into two relations $S(B_1, \, B_2,..., \, B_m)$ and $T(C_1, \, C_2,..., \, C_k)$ such that:

1. $\{A_1, \, A_2,..., \, A_n \} = \{B_1, \, B_2,..., \, B_m \} \cup \{C_1, \, C_2,..., \, C_k \}$.
2. $S = \pi_{B_1, \, B_2,..., \, B_m}(R)$
3. $T = \pi_{C_1, \, C_2,..., \, C_k}(R)$

Example: Let us decompose the *Movies1* relation shown below.

| $title$            | $year$ | $length$ | $genre$ | $studioName$ | $starName$    |
| :----------------- | :----- | :------- | :------ | :----------- | :------------ |
| Star Wars          | 1997   | 124      | sciFi   | Fox          | Carrie Fisher |
| Star Wars          | 1997   | 124      | sciFi   | Fox          | Mark Hamill   |
| Star Wars          | 1997   | 124      | sciFi   | Fox          | Harrison Ford |
| Gone With the Wind | 1939   | 231      | drama   | MGM          | Vivien Leigh  |
| Wayne's World      | 1992   | 95       | comedy  | Paramount    | Dana Carvey   |
| Wayne's World      | 1992   | 95       | comedy  | Paramount    | Mike Meyers   |

Our choice, whose merit will be seen in Section 3.3.3, is to use:

1. A relation called *Movies2*, whose schema is all the attributes except for *starName*.
2. A relation called *Movies3*, whose schema consists of the attributes *title*, *year*, and *starName*.

The projection of *Movies1* onto these two new schemas is shown below:

| $title$            | $year$ | $length$ | $genre$ | $studioName$ |
| :----------------- | :----- | :------- | :------ | :----------- |
| Star Wars          | 1977   | 124      | sciFi   | Fox          |
| Gone With the Wind | 1939   | 231      | drama   | MGM          |
| Wayne's World      | 1992   | 95       | comedy  | Paramount    |

a) The relation *Movies2*.


| $title$            | $year$ | $starName$    |
| :----------------- | :----- | :------------ |
| Star Wars          | 1977   | Carrie Fisher |
| Star Wars          | 1977   | Mark Hamill   |
| Star Wars          | 1977   | Harrison Ford |
| Gone With the Wind | 1939   | Vivien Leigh  |
| Wayne's World      | 1992   | Dana Carvey   |
| Wayne's World      | 1992   | Mike Meyers   |

b) The relation *Movies3*.

Notice how this decomposition eliminates the anomalies we mentioned in Section 3.3.1.

### 3.3.3 Boyce-Codd Normal Form

The goal of decomposition is to replace a relation by several that do not exhibit anomalies. There is, it turns out, a simple condition under which the anomalies discussed above can be guaranteed not to exist. This condition is called `Boyce-Codd normal form`, or `BCNF`.

- A relation $R$ is in BCNF if and only if: whenever there is a nontrivial FD $A_1 A_2 \cdots A_n \rightarrow B_1 B_2 \cdots B_m$ for $R$, it is the case that $\{A_1, \, A_2,..., \, A_n \}$ is a superkey for $R$.

That is, the left side of every nontrivial FD must be a superkey. Recall that a superkey need not be minimal. Thus, an *equivalent statement of the BCNF condition is that the left side of every nontrivial FD must contain a key.*

Example: Relation *Movies1* from above is not in BCNF. To see why, we first need to determine what sets of attributes are keys. We argued in a previous example, that $\{\text{title, year, starName} \}$ is the only key for *Movies1*.

However, consider the FD

$$
\text{title year } \rightarrow \text{ length genre studioName}
$$

which holds in *Movies1* according to a previous discussion.

Unfortunately, the left side of the above FD is not a superkey. Thus, the existence of this FD violates the BCNF condition and tells us *Movies1* is not in BCNF.

### 3.3.4 Decomposition into BCNF

By repeatedly choosing suitable decompositions, we can break any relation schema into a collection of subsets of its attributes with the following important properties:

1. These subsets are the schemas of relations in BCNF.
2. The data in the original relation is represented faithfully by the data in the relations that are the result of the decomposition, in a sense to be made precise in Section 3.4.1.

The decomposition strategy we shall follow is to look for nontrivial FD $A_1 A_2 \cdots A_n \rightarrow B_1 B_2 \cdots B_m$ that violates BCNF, i.e., $\{A_1, \, A_2,..., \, A_n \}$ is not a superkey. We shall add to the right side as many attributes as are functionally determined by $\{A_1, \, A_2,..., \, A_n \}$.

Example: Consider a relation with schema

$$
\{\text{title, year, studioName, president, presAddr}\}
$$

That is, each tuple of this relation tells about a movie, its studio, the president of the studio, and the address of the president of the studio. Three FD's athat we would assume in this relation are

$$
\text{title year } \rightarrow \text{ studioName} \\
\text{studioName } \rightarrow \text{ president} \\
\text{president } \rightarrow \text{ presAddr} 
$$

By closing sets of these five attributes, we discover that $\{\text{title, year} \}$ is the only key for this relation. Thus, the last two FD's above violate BCNF. Suppose we choose to decompose starting with

$$
\text{studioName } \rightarrow \text{ president}
$$

First, we add to the right side of this functional dependency any other attributes in the closure of studioName. That closure includes *presAddr*, so our final choice of FD for the decomposition is:

$$
\text{studioName } \rightarrow \text{ president presAddr}
$$

The decomposition based on this FD yields the following two relation schemas:

$$
\{\text{title, year, studioName} \} \\
\{\text{studioName, president, presAddr} \}
$$

If we use Algorithm 3.12 to project FD's, we determine that the FD's for the first relation has a basis:

$$
\text{title year } \rightarrow \text{ studioName}
$$

while the second has:

$$
\text{studioName } \rightarrow \text{president} \\
\text{president } \rightarrow \text{presAddr}
$$

The sole key for the first relation is $\{\text{title, year} \}$, and it is therefore in BCNF. However, the second has $\{\text{studioName} \}$ for its only key but also the FD:

$$
\text{president } \rightarrow \text{ presAddr}
$$

which is a BCNF violation. Thus, we must decompose again, this time using the above FD. The resulting three relation schemas, all in BCNF, are:

$$
\{\text{title, year, studioName} \} \\
\{\text{studioName, president} \} \\
\{\text{president, presAddr} \}
$$

In general, we must keep applying the decomposition rule as many times as needed, until all our relations are in BCNF.

#### Algorithm 3.20: BCNF Decomposition Algorithm

**INPUT:** A relation $R_0$with a set of functional dependencies $S_0$.

**OUTPUT:** A decomposition of $R_0$ into a collection of relations, all of which are in BCNF.

**METHOD:** The following steps can be applied recursively to any relation $R$ and set of FD's $S$. Initially, apply them with $R=R_0$ and $S=S_0$.
 
1. Check whether $R$ is in BCNF. If som nothing more needs to be done. Return $\{R \}$ as the answer. 
2. If there are BCNF violations, let one be $X \rightarrow Y$. Use Algorithm 3.7 to compute $X^+$. Choose $R_1=X^+$ as one relation schema and let $R_2$ have attributes $X$ and those attributes of $R$ that are not in $X^+$.
3. Use Algorithm 3.12 to compute the sets of FD's for $R_1$ and $R_2$, let these be $S_1$ and $S_2$ respectively.
4. Recursively decompose $R_1$ and $R_2$ using this algorithm. Return the union of the results of these decompositions.
