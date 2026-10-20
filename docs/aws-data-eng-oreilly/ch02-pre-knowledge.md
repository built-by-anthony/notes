# Chapter 2. Prerequisite Knowledge for Aspiring Data Engineers 

## Types of Databases 

### **Hierarchial Databases**

Hierarchial databases store data in a tree kind of structure that maps parent records and child records adn each one of them is treated as a node of the tree. 

### **Relational Databases** 

Relational databases have a simple tabular storage structure where each entity type is treated as a table or view, entities within the tables and views are treated as records, and attributes of the entity are treated as columns of the table or view.

Structured Query Language (SQL) is a programming language that interacts with databases. 

SQL commands are categorized as follows: 

- *Data Definition Language (DDL)*
    - **DDL** helps manage objects and their structures, such as `CREATE` tables, views, indexes, and more.

- *Data Manipulation Language (DML)*
    - **DML** helps modify the data using `INSERT`, `UPDATE`, and `DELETE` statements.

- *Data Query Language (DQL)*
    - **DQL** helps retrieve data from the database using `SELECT` statements. 

- *Data Control Language (DCL)*
    - **DCL** helps database administrators manage authorization on database objects and data in tables using `GRANT` statements. 

- *Transaction Control Language (TCL)*
    - **TCL** helps the database engine make automatic changes to the database, for example reverting erroneous transactions using `ROLLBACK` statements. 

### NoSQL Databases 

When you have a use case that will have variable schema, which means each record may have a different set of attributes, then NoSQL databases are a great fit. 

Some of the popular NoSQL databases types are key value store, document store, graph database, in-memory database, and search database. 

### OLTP Versus OLAP 

OLTP systems primarily perform record-level processing and serve frontend applications that require request responses in a few seconds. Conversely, OLAP systems primarily perform column-level operation and serve analytics and business intelligence (BI) reporting use cases. 