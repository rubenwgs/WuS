# Data Modelling and Databases - Week 1 (Book)
# 2. The Relational Model of Data
## 2.1 An Overview of Data Models
### 2.1.1 What is a Data Model?
A `data model` is a notation for describing data or information. The description generally consists of three parts:
1. *Structure of the data*: In the database world, data models are at somewhat higher level than data structures, and are sometimes referred to as *conceptual model* to emphasize the difference in level.
2. *Operations on the data*: We are generally allowed to perform a limited set of `queries` (operations that retrieve information) and `modifications` (operations that cahnge the database).
3. *Constraints on the data*: Database data models usually have a way to describe limitations on what the data can be.

### 2.1.3 The Relational Model in Brief
The relational model is based on tables. The structure portion of the relational model migh appear to resemble an array of structs in C, where the column headers are the field names, and each of the rows represent the values of one struct in the array.

## 2.2 Basics of the Relational Model
The relational model gives us a single way to represent data: as a two-dimensional table called a `relation`.

In this section, we shall introduce the most important terminology regarding relations, and illustrate them with the *Movies* relation:

| `title`            | `year` | `length` | `genre` |
| :----------------- | :----: | :------: | :-----: |
| Gone With the Wind | 1939   | 231      | drama   |
| Star Wars          | 1977   | 124      | sciFi   |
| Wayne's World      | 1992   | 95       | comedy  |

*Table 2.3: The realtion 'Movies'*

### 2.2.1 Attributes
The columns of a relation are named by `attributes`, in Tab. 2.3 the attributes are *title, year, length* and *genre*.

### 2.2.2 Schemas
The name of a relation and the set of attributes for a relation is called the `schema`for that relation. Thus, the schema for relation *Movies* as above is

> Movies(title, year, length genre)

The set of schemas for the relations of a database is called a `relational database schema`, or just `database schema`.

### 2.2.3 Tuples
The rows of a relation, other than the header row containing the attribute names, are called `tuples`. A tuple has one compinent for each attribute of the relation. <br>
For example,

> (Gone With the Wind, 1939, 231, drama)

is the first tuple of Tab. 2.3.

### 2.2.4 Domains
The relational mode requires that each component of each tuple to be *atomic*, that is, it must be of some elementary type such as integer or string. <br>
It is further assumed that associated with each attribute of a relation is a `domain`, that is, a particular elementary type.

It is possible to include the domain, or data type, for each attribute in a relation schema. We shall do so by appending a colon and a type after attributes:

> Movies(title:string, year:integer, length:integer, genre:string)

### 2.2.5 Equivalent Representations of a Relation
One important remark is that relations are sets of tuples, not lists of tuples. Thus *the order in which the tuples of a relation are represented is immaterial.*

### 2.2.6 Relation Instances
A relation about movies is not static, rather, relations change over time. It is less common for the schema of a relation to change though. However, there are situations where we might want to add or delete attributes. <br>
Schema changes, while possible in commercial database systems, can be very expensive, because each of perhaps millions of tuples need to be rewritten to add or delete components.

We shall call a set of tuples for a given relation an `instance` of that relation. For example, in 1990, the relation *Movies* did not contain the tuple for *Wayne's World*. However, a conventional database system maintains only one version of any relation: the set of tuples that are in the relation "now". This instance of the relation is called the `current relation`.

### 2.2.7 Keys of Relations
There are many constraints on relations that the relational model allows us to place on database schemas. However, one kind of constraint is so fundamental that we shall introduce if there: `key constraints`. A set of attributes forms a `key` for a relation if we do not allow two tuples in a relation instance to have the same values in all the attributes of the key.

We indicate the attribute or attributes that form a key for a relation ny underlining the key attribute(s). For example, the *Movies* relation could have its schema written as:

> Movies(<ins>title</ins>, <ins>year</ins>, length, genre)

While we might be sure that *title* and *year* can serve as a key for *Movies*, many real-world databses use artificial keys, doubting that it is safe to make any assumption about the values of attributes outside their control.