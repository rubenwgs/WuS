# Data Modelling and Databases - Chapter 10 (Book)
- Author: Ruben Schenk
- Date: 13.05.2021
- Contact: ruben.schenk@inf.ethz.ch

# 10. Advanced Topics in Relational Databases
## 10.1 Security and User Authorization in SQL
SQL postulates the existence of `authorization ID's`, which are essentially user names. SQL also has a special authorization ID called `PUBLIC`, which includes any user. Authroiztaion ID's may be granted privileges, much as they would be in the file system environment maintained by an operating system.

### 10.1.1 Privileges
SQL defines nine types of privileges: SELECT, INSERT, DELETE, UPDATE, REFERENCES, USAGE, TRIGGER, EXECUTE, and UNDER.

The first four of these apply to a relation, which may be either a base table or a view. As their names imply, they give the holder of the privilege the right to query (select from) the relation, insert into the relation, delete from the relation, and update tuples of the realtion, respectively.  
SELECT, INSERT, and UPDATE may also have an associated list of attributes, for instance, SELECT(name, addr). If so, then only those attributes may be seen in a selection, specified in an insertion, or changed in an update, respectively.

The REFERENCES privilege on a relation is the right to refer to that relation in an integrity constraint. These constraints may take any of the forms mentioned in Chapter 7, such as assertions, attribute- or tuple-based checks, or referential integrity constraints. The REFERENCES privilege may also have an attached list of attributes, in which case only those attributes may be referenced in a constraint.

USAGE is aprivilege that applies to several kinds of schema elements other than relations and assertions. It is the right to use that element in one's own declarations.

The TRIGGER privilege on a relation is the right to define triggers on that relation.

EXECUTE is the right to execute a piece of code, such as a PSM procedure or function.

Finally, UNDER is the right to create subtypes of a given type.

### 10.1.2 Creating Privileges
There are two aspects to the awarding of privileges: how they are created initially, and how they are passed from user to user.  
First, SQL elements such as schemas or modules have an owner. The owner of something has all privileges associated with that thing. There are three points at which ownership is established in SQL:
1. When a schema is created, it and all the tables and other schema elements in it are owned by the user who created it.
2. When a session is initiated by a CONNECT statement, there is an opportunity to indicated the user with an AUTHORIZATION clause.
3. When a module is created, there is an option to give it an owner by using an AUTHORIZATION clause.

