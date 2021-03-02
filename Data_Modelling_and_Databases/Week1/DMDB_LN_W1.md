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

```Movies(title, year, length genre)```

