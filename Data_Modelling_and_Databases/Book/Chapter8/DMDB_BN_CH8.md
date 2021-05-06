# Data Modelling and Databases - Chapter 8 (Book)
- Author: Ruben Schenk
- Date: 05.05.2021
- Contact: ruben.schenk@inf.ethz.ch

# 8. Views and Indexes
We begin this chapter by introducing virtual views, which are relations that are defined by a query over other relations. Virtual views are not stored in the database, but can be queried as if they existed. The query processor will replace the view by its definition in order to execute the query.

Views can also be materialized, in the sense that they are constructed periodically from the database and stored there. The existence of these materialized views can be speed up the execution of queries.

## 8.1 Virtual Views
Relations that are defined with a CREATE TABLE statement actually exist in the database. They are *persistent*, in the sense that they can be expected to exist indefinitely and not to change unless they are explicitely told to change by a SQL modification statement.

There is another class of SQL relations, called `(virtual) views`, that do not exist physically. Rather, they are defined by an expression much like a query.

### 8.1.1 Declaring Views
The simplest form of `view definition` is:

```sql
    CREATE VIEW <view-name> AS <view-definition>;
```

The view definition is an SQL query.

Example: Suppose we want to have a view that is part of the *Movies* relation, specifically, the titles and yyears of the movies made by Paramaount Studios. We can define this view by:

```sql
    /* Code 8.1: Views in SQL. */
    CREATE VIEW ParamountMovies AS
        SELECT title, year
        FROM Movies
        WHERE studioName = 'Paramount';
```

### 8.1.2 Querying Views
A view may be queried exactly as if it were a stored table. We mention its name in a FROM clause and rely on the DBMS to produce the needed tuples by operating on the relations used to define the virtual view.

Example: We may query the view *ParamountMovies* just as if it were a stored table, for instance:

```sql
    /* Code 8.3: Querying virtual views. */
    SELECT title
    FROM ParamountMovies
    WHERE year = 1979;
```

finds the movies made by Paramount in 1979.

### 8.1.3 Renaming Attributes
Sometimes, we might prefer to give a view's attributes names of our own choosing. We may specify the attributes of the view by listing them, surrounded by parentheses, after the name of the view in the CREATE VIEW statement. 

Example: For instance, we could write:

```sql
    CREATE VIEW MovieProd(movieTitle, prodName) AS
        SELECT title, name
        FROM Movies, MovieExec
        WHERE producerNum = certNum;
```

## 8.2 Modifying Views
In limited circumstances it is possible to execute an insertion, deletion, or update to a view. At first, this idea makes no sense at all, since the view does not exist the way a base table (stored relation) does.

For many views, it is simply not possible to do that. However, for sufficiently simple views, called `updatable views`, it is possible to translate modification of the view into an equivalent modification on the base table, and the modification can be done to the base table instead.

### 8.2.1 View Removal
An extrem modification of a view is to `delete` it altogether. This modification may be done whether or not the view is updatable. A typical `DROP` statement is

```sql
    DROP VIEW ParamountMovies;
```

Note that this statement *deletes the definition of the view*, so we may no longer make queries or issue modification commands involving this view. However dropping the view does not affect any tuples of the underlying relation *Movies*.

### 8.2.2 Updatable View
SQL provides a formal definition of when modifications to a view are permitted. The SQL rules are complex, but roughly, they permit modifications on views that are defined by selecting (using SELECT, not SELECT DISTINCT) some attributes from one Relation $R$. Some important technical points:
- The WHERE clause must not involve $R$ in a subquery.
- The FROM clause can only consist of one occurrence of $R$ and no other relation.
- The list in the SELECT clause must include enough attributes that for every tuple inserted into the view, we can fill the other attributes out with NULL values or the proper default.

Example: Suppose we insert into view *ParamountMovies* a tuple like:

```sql
    /* Code 8.5: Inserting into views. */
    INSERT INTO ParamountMovies
    VALUES('Star Trek', 1979);
```

Theinsertion on *ParamountMovies* is executed as if it were the same insertion on *Movies*:

```sql
    /* 8.5: Continuation. */
    INSERT INTO Movies(title, year)
    VALUES('Star Trek', 1979);
```