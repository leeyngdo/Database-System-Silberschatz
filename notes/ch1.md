---
title:  "[DB] Introduction to Database System" 
excerpt: ""
categories:
  - Database System
tags:
  - [Computer Science, DB]
header:
  teaser: "/assets/images/database-system/teaser/database-system.jpg"

toc: true
toc_sticky: true

date: 2024-02-26
last_modified_at: 2024-02-26

lang: en
ref: introDB
use_math: true
---

<strong>Database</strong> is a collection of data that contains information about an enterprise. Since information is so crucial in most applications, there is a large body of concepts and techniques for managing data. And <strong>database system</strong>, a collection of interrelated data and a set of programs to access those data, provides a way to managing database conveniently and efficiently. 

# Purpose of Database

In the early days, database applications were built directly on top of file-processing systems that stores permanent records in various files, which leads to:

<ul>
  <li>
    <strong>Data redundancy and inconsistency</strong>
    <p>Data is stored in multiple file formats. This leads to resulting in duplication of information in different files (<strong>redundancy</strong>) and that the various copies of the same data may no longer agree (<strong>inconsistency</strong>). </p>
  </li>
  <li>
    <strong>Difficulty in accessing data</strong>
    <p>Conventional file-processing environments do not allow needed data to be retrieved in a convenient and efficient manner. A new program needs to be implemented to carry out each new retrieval task.</p>
  </li>
  <li>
    <strong>Data isolation</strong>
    <p>Because data are scattered in various files, and files may be in different formats, writing a program to retrieve the appropriate data is difficult.</p>
  </li>
  <li>
    <strong>Integrity problems</strong>
    <p><strong>Integrity constraints</strong> (e.g., <code>account balance > 0</code> when the university maintains an account for each department and  records the balance amount in each account) become "buried" in program code rather than being stated explicitly. Hence, it is hard to add new constraints or change existing ones.</p>
  </li>
  <li>
    <strong>Atomicity of updates</strong>
    <p>Failures may leave database in an inconsistent state with partial updates carried out, e.g. Transfer of funds from one account to another must be <strong>atomic</strong>; either complete or not happen at all</p>
  </li>
  <li>
    <strong>Concurrent access by multiple users</strong>
    <p>Concurrent access is necessitated for high performance. Uncontrolled concurrent accesses can lead to inconsistencies, e.g. Two people reading a balance (say 100) and updating it by withdrawing money (say 50 each) at the same time.</p>
  </li>
  <li>
    <strong>Security problems</strong>
    <p>Not every user of the database system should be able to access all the data. But it is hard to provide user access to some, but not all, data in conventional file-processing systems.</p>
  </li>
</ul>

<strong>Database systems</strong> offer solutions to all the above problems. 

<br>

# View of Data

A <strong>database system</strong> is a collection of interrelated data and a set of programs that allow users to access and modify these data. A major purpose of a database system is to provide users with an <strong>abstract</strong> view of the data. That is, the system hides certain details of how the data are stored and maintained.

## Data Models

The <strong>data model</strong> is a collection of conceptual tools for describing <strong>data</strong>, <strong>data relationships</strong>, <strong>data semantics</strong>, and <strong>consistency constraints</strong>. The data models can be classified into 4 different categories:

<ul>
  <li>
    <strong>Relational model</strong>
    <p>All the data is stored in various tables. Each table has multiple columns, and each column has a unique name. Tables are also known as <strong>relations</strong>.</p>
  </li>
  <li>
    <strong>Entity-Relationship (E-R) model</strong>
    <p>Uses a collection of basic objects, called <em>entities</em>, and <em>relationships</em> among these objects.</p>
  </li>
  <li>
    <strong>Semi-structured data model</strong>
    <p>Permits the specification of data where individual data items of the same type may have different sets of attributes.</p>
  </li>
  <li>
    <strong>Object-based data model</strong>
    <p>Allow procedures to be stored in the database system and executed by the database system.</p>
  </li>
</ul>

A large portion of future posts will be focused on the relational model because it serves as the foundation for most database applications.

## Data Abstraction

The complexity of the data structures to represent data in the database is hidden from users through several levels of <strong>data abstraction</strong> to simplify users' interactions with the system:

<ul>
  <li>
    <strong>Physical level</strong>
    <p>Describes <em>how</em> the data are actually stored. It describes complex low-level data structures in detail.</p>
  </li>
  <li>
    <strong>Logical level</strong>
    <p>Describes <em>what</em> data are stored in the database, and <em>what</em> relationships exist among those data.</p>
  </li>
  <li>
    <strong>View level</strong>
    <p>Describes only part of the entire database. The view level of abstraction exists to simplify users’ interaction with the system. The system may provide many views for the same database as many users of the database system do not need all this information.</p>
  </li>
</ul>

<p align = "center">
<img width="700" alt="image" src="/assets/images/database-system/introduction/data-abstraction.png">
<figcaption style="text-align: center;">$\mathbf{Fig\ 1.}$ The three levels of data abstraction. </figcaption>
</p><br>

## Instances and Schemas

Databases change over time as information is inserted and deleted. 


<ul>
  <li>
    <p>The collection of information stored in the database at a particular moment is called an <strong>instance</strong> of the database.</p>
  </li>
  <li>
    <p>The overall design of the database is called the database <strong>schema</strong>. Database systems have several schemas, partitioned according to the levels of abstraction.</p>
    <ul>
      <li>
        <strong>Physical schema</strong>
        <p>Describes the overall physical structure of the database.</p>
      </li>
      <li>
        <strong>Logical schema</strong>
        <p>Describes the overall logical structure of the database.</p>
      </li>
      <li>
        <strong>Subschema</strong>
        <p>Describes different views of the database.</p>
      </li>
    </ul>
  </li>
  <li>
    <p><em>Analogy</em>: A database schema corresponds to the variable declarations in a program, and the values of the variables in a program at a point in time correspond to an instance of a database schema.</p>
  </li>
</ul><br>

The ability to modify the physical schema without changing the logical schema is called <strong>Physical data independence</strong>. In general, the interfaces between the various levels and components should be well defined, so that changes in some parts do not seriously influence others.

# Reference
**[1]** [Silberschatz, Abraham, Henry F. Korth, and Shashank Sudarshan. "Database system concepts." (2011).](https://db-book.com/) <br>
