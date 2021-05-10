# Data Modelling and Databases - Chapter 7 (Book)
- Author: Ruben Schenk
- Date: 10.05.2021
- Contact: ruben.schenk@inf.ethz.ch

# 7. Constraints and Triggers
In this chapter we shall cover those aspects of SQL that let us create *active* elements. An `active` element is an expression or statement that we write once and store in the database, expecting the element ot execute at appropriate times.

SQL provides a variety of techniques for expressing `integrity constraints` as part of the database schema. In this chapter we shall study the principal methods.

## 7.1 Keys and Foreign Keys
Recall from Section 2.3.6 that SQL allows us to define an attribute or attributes to be a key for a relation with the keywords `PRIMARY KEY` or `UNIQUE`. SQL also uses the term *key* in connection with certain referential-integrity constraints.

These constraints, called `foreign-key constraints`, assert that a value appearing in one relation must also appear in the primary-key component(s) of another relation.

### 7.1.1 Declaring Foreign-Key Constraints
A foreign key cosntraint is an assertion that values for certain attributes must make sense. In SQL we may decalre an attribute or attributes of one relation to be a `foreign key`, referencing some attribute(s) of a second relation. The implication of this declaration is twofold:
1. The referenced attribute(s) of the second realtion must be declared UNIQUE or the PRIMARY KEY for this relation.
2. Values of the foreign key appearing in the first relation must also appear in the referenced attributes of some tuple.

As for primary keys, we have two ways to declare a foreign key:
1. If the foreign key is a single attribute we may follow its name and type by a declaration that it *references" some attribute of some talbe. The form of the declaration is

```sql
    REFERENCES <table>(<attribute>)
```

2. Alternatively, we may append to the list of attributes in a CREATE TABLE statement one or more declarations stating that a set of attributes is a foreign key. The form of this declaration is:

```sql
    FOREIGN KEY (<attributes>) REFERENCES <table>(<attributes>)
```

Example: Suppose we wish to declare the relation `Studio(name, address, presCNum)` whose primary key is *name* and which has a foreign key *presCNum* that references *certNum* of the relation `MovieExec(name, address, certNum, netWorth)`. We may declare *presCNum* directly to reference *certNum* as follows:

```sql
    /* Code 7.1: Foreign keys in SQL. */
    CREATE TABLE Studio (
        name CHAR(30) PRIMARY KEY,
        address VARCHAR(255),
        presCNum INT REFERENCES MovieExec(certNum)
    );
```

An alternative form is to add the foreign key declaration separately, as

```sql
    /* Code 7.1: Continuation. */
    CREATE TABLE Studio (
        name CHAR(30) PRIMARY KEY,
        address VARCHAR(255),
        presCNum INT,
        FOREIGN KEY (presCNum) REFERENCES MovieExec(certNum)
    );
```

### 7.1.2 Maintaining Referential Integrity
Referring to example 7.1 from above, the DBMS will prevent the following actions:
- We try to insert a new *Studio* tuple whose *presCNum* value is not *NULL* and is not the *certNum* component of any *MovieExec* tuple.
- We try to update a *Studio* tuple to change the *presCNum* component to a non-NULL value that is not the *certNum* component of any *MovieExec* tuple.
- We try to delete a *MovieExec* tuple, and its *certNum* component, which ius not NULL, appears as the *presCNum* component of one or more *Studio* tuples.
- We try to update a *MovieExec* tuple in a way that changes the *certNum* value, and the old *certNum* is the value of *presCNum* of some movie studio.

For the first two modifications, where the change is to the relation where the foreign-key constraint is declared, there is no alternative. The system has to reject the violating modification.

For changes to the referenced relation, of which the last two modifications are examples, the designer can choose among three options:
1. `The Default Policy: Reject Violating Modifications`. SQL has a default policy that any modification violating the referential integrity constraint is rejected.
2. `The Cascade Policy`. Under this policy, changes to the referenced attribute(s) are mimicked at the foreign key.
3. `The Set-Null Policy`. Here, when a modification to the referenced relation affects a foreign-key value, the latter is changed to NULL.

These options may be chosen for deletes and updates, independently, and they are stated with the declaration of the foreign key. We decalre them with `ON DELETE` or `ON UPDATE` followed by our choice of `SET NULL` or `CASCADE`.

Example: We might modify our code example 7.1 the following way:

```sql
    /* Code 7.2: Policy on changes to referenced relations. */
    CREATE TABLE Studio (
        name CHAR(30) PRIMARY KEY,
        address VARCHAR(255),
        presCNum INT REFERENCES MovieExec(certNum)
            ON DELETE SET NULL
            ON UPDATE CASCADE
    );
```

### 7.1.3 Deferred Checking of Constraints
Assume the situation of code example 7.1.Now, Arnold Schwarzenegger decides to found a movie studio, called *La Vista Studios*, of which he will naturally be the president. If we execute the insertion

```sql
    INSERT INTO Studio
    VALUES('La Vista', 'New York', 23456);
```

we are in trouble. The reason is that there is no tuple of *MovieExec* with certificate number $23456$, so there is an abvious violation of the foreign-key constraint.  
One possible fix is to first insert the tuple for *La Vista* without a president's certificate, as:

```sql
    INSERT INTO Studio(name, address)
    VALUES('La Vista', 'New York');
```
This change avoids the constraint violation. However, we must insert a tuple for Arnold Schwarzenegger into *MovieExec*, with his correct certificate number before we can apply an update statement such as

```sql
    UPDATE Studio
    SET presCNum = 23456
    WHERE name = 'La Vista';
```

Of course, inserting Arnold Schwarzenegger and his certificate number into *MovieExec* before inserting *La Vista* into *Studio* will surely protect against a foreign-key violation in this case. However, there are cases of `circular constraints` that cannot be fixed by judiciously ordering the database modification steps we make.

The problem shown in the example above can be solved as follows:
1. First, we must group the two insertions (one into *Studio* and the other into *MovieExec*) into a single transactions.
2. Then, we need a way to tell the DBMS not to check the constraints until after the whole transaction has finished its actions and is about to commit.

To inform the DBMS about point $(2)$, the declaration of any constraint (e.g., key, foreign-key, etc.) may be followed by one of `DEFERRABLE` or `NOT DEFERRABLE`. The later is the default, and means that every time a database modification statement is executed, the constraint is checked immediately afterwards. However, if we declare a constraint to be DEFERRABLE, then we have the option of having it wait until a transaction is complete before checking the constraint.

We follow the keyword DEFERRABLE by either `INITIALLY DEFERRED` or `INITIALLY IMMEDIATE`. In the former case, checking will be deferred to just before each transaction commits. In the latter case, the check will be made immediately after each statement.