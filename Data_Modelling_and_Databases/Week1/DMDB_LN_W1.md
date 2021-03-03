# Data Modelling and Databases - Week 1 (Book)
- Author: Ruben Schenk
- Date: 02.03.2021
- Contact: ruben.schenk@inf.ethz.ch


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

We distinguish further between `candidate keys`, which is the minimal set of fields that identify each tuple uniquely, and `primary key`, which is one chosen candidate key, marked in the schema by underlining it.

We indicate the attribute or attributes that form a key for a relation ny underlining the key attribute(s). For example, the *Movies* relation could have its schema written as:

> Movies(<ins>title</ins>, <ins>year</ins>, length, genre)

While we might be sure that *title* and *year* can serve as a key for *Movies*, many real-world databses use artificial keys, doubting that it is safe to make any assumption about the values of attributes outside their control.

> Short summaries of some keywords:
> - Relation: 2D representation of data
> - Database schema: A set of relation schemas
> - Relation schema: Name and a set of attributes (or fields or columns)
> - Attribute: Name and domain (e.g., integer, string, etc.)
> - Tuples: A row of a relation containing the attribute names
> - Key: Set of attributes that uniquely identify each tuple

## 2.3 Defining a Relation Schema in SQL
SQl is the principal language used to describe and manipulate relational databases. There are two aspects to SQL:
1. The `Data-Definition` sublanguage for declaring database schemas and
2. The `Data-Manipulation` sublanguage for querying databases and for modifying the database.

### 2.3.1 Relations in SQL
SQL makes a distinction between three kinds of relations.
1. Stored relations, which are called `tables`. These are the kind of relations we deal with ordinarily - a relation that exists in the database.
2. `Views`, which are relations defined by computation. These relations are not stored, but are constructed when needed.
3. `Temporary tables`, which are constructed by the SQL language processor when it performs its job of executing queries and data modifications.

### 2.3.2 Data Types
Let us introduce the primitive data types that are supported by SQL systems:
- `CHAR(n)` denotes a fixed-length string of up to $n$ characters. `VARCHAR(n)` also denotes a string of up to $n$ characters, with the difference being, that `CHAR` implies that store strings are padded to make $n$ characters.
- `BIT(n)` denotes a bit of strings of length $n$ and `BIT VARYING(n)` denotes bit strings of length up to $n$.
- `BOOLEAN` denotes an attribute whose value is logical. It can either be `TRUE`, `FALSE`, or `UNKNOWN`.
- `INT` or `INTEGER` denotes typical integer values.
- `FLOAT` or `REAL` are used for typical floating-point numbers. A higher precision can be obtained with the type `DOUBLE PRECISION`. `DECIMAL(n, d)` allows values that consist of $n$ decimal digits, with the decimal point assumed to be $d$ positions from the right.
- `DATE` and `TIME` are used to represent dates and times respectively. Dates are a quoted string of the form `'2021-03-02'` and times is a quoted string of the form `'13:52:31.5'`, where we may continue with a decimal point and as many significant digits as we want.

### 2.3.3 Simple Table Declarations
The simplest form of declaration of a relational schema consists of the key-words `CREATE TABLE` as shown below:

```SQL
    /* Code 2.7: SQL declaration of the table 'Movies' */
    CREATE TABLE Movies (
        title           CHAR(100),
        releaseYear     INT,
        lengthMin       INT,
        genre           CHAR(10),
        studioName      CHAR(30),
        producerNum     INT
    );
```

### 2.3.4 Modifying Relation Schemas
We can delete a relation $R$ by the SQL statement `DROP TABLE R;`. <br>
More frequently than we would drop a relation, we may need to modify the schema of an existing relation. These modifications are done by a statement that begins with the keyword `ALTER TABLE` and the name of the relation. We then have several options, the most important of which are
- `ADD` followed by an attribute name and its data type
- `DROP` followed by an attribute name

For example we could modify the *Movie* relation by adding an attribute *rating* with:

```sql
    ALTER TABLE Movies ADD rating INT;
```

As another example, the `ALTER TABLE` statement:

```sql
    ALTER TABLE Movies DROP studioName;
```

deletes the *studioName* attribute.

### 2.3.5 Default Values
When we create or modify tuples, we sometimes do not have values for all components. In general, any palce we decalre an attribute and its data type, we may add the keyword `DEFAULT`and an appropriate value. The value is either `NULL` or a constant.

As an example, we might want to use *?* as a default for *studioName* and *NULL* as a default *producerNum*. We could replace the declarations of *studioName* and *producerNum* in Code 2.7 by:

```sql
    studioName      CHAR(30) DEFAULT '?',
    producerNum     INT DEFAULT 0
```

### 2.3.6 Declaring Keys
There are two way to declare an attribute or set of attributes to be a key in the `CREATE TABLE` statement that defines a stored relation:
1. We may declare one attribute to be a key when that attribute is listed in the relation schema.
2. We may add to the list of items declared in the schema an additional declaration that say a particular attribute or set of attributes forms the key.

Remark: If the key consists of more than one attribute, we have to use method (2).

There are two declarations that may be used to indicate keyness:
- `PRIMARY KEY`, or
- `UNIQUE`

If `PRIMARY KEY` is used, then attributes in $S$, where $S$ is the set of keys, are not allowed to have *NULL* as a value. *NULL* is permitted if the set $S$ is declared as `UNQIUE`, however.

```sql
    /* Code 2.9: Making 'fullName' the key. */
    CREATE TABLE MovieStar (
        fullName    CHAR(30) PRIMARY KEY,
        address     VARCHAR(255),
        gender      CHAR(1),
        birthdate   DATE
    );

    /* Code 2.11: Making 'title' and 'releaseYear' be the key of 'Movies'. */
    CREATE TABLE Movies (
        title           CHAR(100),
        releaseYear     INT,
        lengthMin       INT,
        genre           CHAR(10),
        studioName      CHAR(30),
        producerNum     INT,
        PRIMARY KEY (title, releaseYear)
    );
```
