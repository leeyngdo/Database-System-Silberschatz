---
title:  "[DB] Intermediate SQL" 
excerpt: ""
categories:
  - Database System
tags:
  - [Computer Science, DB]
header:
  teaser: "/assets/images/database-system/teaser/sql-inter.png"

toc: true
toc_sticky: true

date: 2024-03-14
last_modified_at: 2024-03-14

lang: en
ref: interSQL
use_math: true
---

# Join Expressions

Instead of the Cartesian product, a number of <strong>join</strong> operations allow the programmer to write some queries in a more natural way and to express some queries that are difficult to do with only the Cartesian product.

## Inner Join

The <strong>inner join</strong> returns only those rows that match/exist in both tables, i.e. do not preserve nonmatched tuples.

```sql
SELECT columns
FROM table1 
INNER JOIN table2 /* or simply join depending on a DBMS */
ON table1.column = table2.column
```

The followings are equivalent:

```sql
/* Explicit: Preferred style */
SELECT employees.employee_id, employees.last_name, positions.title
FROM employees 
INNER JOIN positions
ON employees.position_id = positions.position_id

/* Implicit: Old style (Not preferred) */
SELECT employees.employee_id, employees.last_name, positions.title
FROM employees, positions
WHERE employees.position_id = positions.position_id
```

## Natural Join

The <strong>natural join</strong> operation operates on two relations and produces a relation as a result. It matches tuples with the same values for <strong>all common attributes</strong>, and retains only one copy of each common attribute.

```sql
r_1 NATURAL JOIN r_2 NATURAL JOIN ... NATURAL JOIN r_m
```

Unlike the Cartesian product of two relations, natural join considers only those pairs of tuples with the same value on those attributes that appear on the schema of both relations. It is an <strong>equi-join</strong> which occurs <strong>implicitly</strong> by comparing all the same name columns in both relations.

<p align = "center">
<img width="400" alt="image" src="/assets/images/database-system/intermediate/sqlite-natural-join.png">
<figcaption style="text-align: center;">$\mathbf{Fig\ 1.}$ Illustration of natural join (<a href="https://www.w3resource.com/sqlite/sqlite-natural-join.php">w3resource</a>) </figcaption>
</p><br>

Hence, the followings are equivalent

```sql
--- Cartesian Product
SELECT name, course_id
FROM students, takes
WHERE student.ID = takes.ID
--- Natural Join
SELECT name, course_id
FROM students NATURAL JOIN takes;
```

It is a type of the <strong>equi-join</strong> which occurs implicitly by comparing all the same name columns in both tables.

## Join conditions

To prevent equating attributes erroneously, SQL supports the specification of the join condition.

### <code>USING</code> construct

The operation <code>(INNER) JOIN ... USING</code> requires a list of attribute names to be specified.

```sql
r_1 INNER JOIN r_2 USING(A_1, ... , A_n)

--- or (depends on DBMS)

r_1 JOIN r_2 USING(A_1, ... , A_n)
```

* <code>r_i</code>: Relation
* <code>A_i</code>: Attribute

The operation is equivalent to:

```sql
SELECT  *
FROM    r_1,
        r_2
WHERE   COALESCE (r_1.A_1, r_2.A_1) AS A_1
AND     ...
AND     COALESCE (r_1.A_n, r_2.A_n) AS A_n
```

* e.g., List the names of students along with the titles of courses that they have taken

```sql
--- Without USING
SELECT name, title
FROM student NATURAL JOIN takes, course
WHERE takes.course_id = course.course_id;
--- With USING
SELECT name, title
FROM (student NATURAL JOIN takes) INNER JOIN course USING (course_id);
```

It is an <strong>equi-join</strong>; a special type of the inner join in which we use only an equality operator, and causes duplicate attributes to be <strong>removed</strong> from the result set.

<br>

### <code>ON</code> construct

The <strong><code>ON</code></strong> condition allows a general predicate over the relaions being joined. This predicate is written like a <code>WHERE</code> clause predicate:

```sql
r_1 /* INNER */ JOIN r_2 ON p
```

* <code>r_i</code>: Relation
* <code>p</code>: Predicate

hence the operation is equivalent to:

```sql
SELECT  *
FROM    r_1,
        r_2
WHERE   p
```

Therefore it becomes a <strong>theta join</strong>; a special type of the inner join in which arbitrary comparison relationships (such as $\geq$) are allowed, and it allows duplicate attributes to appear in the result set.

* e.g., All attributes about all students, along with all the courses that they have took

    ```sql
    SELECT  *
    FROM    student
    JOIN    takes
    ON      student.ID = takes.ID
    ```

### Comparison of <code>ON</code> and <code>USING</code>

In a nutshell, <code>ON</code> can be used for most joins, but <code>USING</code> is a handy shorthand for the situation where the column names are the same. The following queries are equivalent.

```sql
--- Using ON
SELECT  I.title, R.name
FROM    albums I
INNER JOIN  artists R
ON      R.artist_id = I.artist_id;

--- Using USING
SELECT  title, name
FROM    albums
INNER JOIN  artists
USING (artist_id);
```

## Outer Join

The <strong>outer join</strong> is an extension of the join operation that <strong>avoids loss of information</strong>. For example, 

```sql
SELECT *
FROM student NATURAL JOIN takes
```

might exclude some student who takes no courses. The outer-join computes the join operation, and then adds tuples from one relation that does not match tuples in the other relation to the result of the join, using NULL values.

There are 3 forms of outer join:

* <strong><code>LEFT OUTER JOIN</code></strong><br>
  Preserves tuples only in the relation named to the left of the <code>LEFT OUTER JOIN</code> operation.
* <strong><code>RIGHT OUTER JOIN</code></strong><br>
  Preserves tuples only in the relation named to the right of the <code>RIGHT OUTER JOIN</code> operation.
* <strong><code>FULL OUTER JOIN</code></strong><br>
  Preserves tuples in both relations.

In constrast, the join operations that do not preserve nonmatched tuples are called <code>INNER JOIN</code> operations.

<p align = "center">
<img width="600" alt="image" src="/assets/images/database-system/intermediate/sql-join-visualisation.png">
<figcaption style="text-align: center;">$\mathbf{Fig\ 2.}$ Illustration of join operations </figcaption>
</p><br>

<p align = "center">
<img width="600" alt="image" src="/assets/images/database-system/intermediate/outer-join-comparison.png">
<figcaption style="text-align: center;">$\mathbf{Fig\ 3.}$ Outer Join comparisons </figcaption>
</p><br>

### Join Condition for Outer Join

* <strong><code>table1 NATURAL [LEFT | RIGHT | FULL] OUTER JOIN table2</code></strong>
    * e.g., Find all students who have not taken a course

        ```sql
        SELECT ID 
        FROM student NATURAL LEFT OUTER JOIN takes
        WHERE course_id is NULL;
        ```
* <strong><code>table1 NATURAL [LEFT | RIGHT | FULL] OUTER JOIN table2 ON p</code></strong>
* <strong><code>table1 NATURAL [LEFT | RIGHT | FULL] OUTER JOIN table2 USING (A_1, ..., A_n)</code></strong>

<br>

<strong>Example.</strong><br>
Suppose that the relation <code>student</code> has a tuple with name <code>Snow</code>, which has no corresponding tuples in the <code>takes</code> relation. The following two queries are <strong><em>not</em></strong> equivalent.

```sql
SELECT  *
FROM    student
LEFT OUTER JOIN takes
ON      student.ID = takes.ID;
WHERE   student.name = 'Snow'
-- Result: (70557, 'Snow', 'Physics', 0, NULL, NULL, NULL, NULL, NULL, NULL)

--- NOT EQUIVALENT

SELECT  *
FROM    student
LEFT OUTER JOIN takes
/* ON student.ID = takes.ID; */
WHERE   student.ID = takes.ID
AND     student.name = 'Snow'
-- Result: (empty)
```

<br>

In the latter query, every tuple satisfies the join condition vacuously, hence the outer join actually generates the Cartesian product of the two relations. Since there are no tuples in <code>takes</code> with the ID <code>'70557'</code>, every time a tuple appears in the outer join with the name <code>'Snow'</code>, the values for <code>student.ID</code> and <code>takes.ID</code> must be different, and such tuples are eliminated by the <code>WHERE</code> clause predicate. Thus the resultset is empty.

<br>

# Views

In some cases, it is not desirable for all users to see the entire <strong>logical model</strong> (i.e., all the actual <strong>relations</strong> stored in the database). For example, consider a person who needs to know an instructor name and his/her department, but not his/her salary; this person should see a relation described in SQL:

```sql
SELECT ID, name, dept_name
FROM instructor
```

Aside from security concerns, one may wish to create personalized collection of relations that is better matched to a certain user’s intuition than is the logical model. In SQL, a <strong>view</strong> provides a mechanism to hide certain data from the view of certain users.

SQL allows a <strong>virtual relation</strong> to be defined by a query, and the relation conceptually contains the result of the query. Any such relation that is not part of the logical model, but is made visible to a user as a virtual relation, is called a <strong>view</strong>.

<br>

## View Definition

A view is defined using the <strong><code>CREATE VIEW</code></strong>. 

```sql
CREATE VIEW v AS Q
```

* <code>v</code>: The name of the view
* <code>Q</code>: Any legal SQL expression

Once a view is defined, the view name can be used to refer to the virtual relation that the view generates.

View denition is <strong>not</strong> the same as creating a new relation by evaluating the query expression. Rather, a view definition causes the saving of an expression; the expression is substituted into queries that is using the view, which is called <strong>query modification</strong>. Also views differ from the <code>WITH</code> statement in that views, once created, remain available until explicitly dropped. The named subquery defined by <code>WITH</code> is local to the query in which it is defined.

* e.g., A view of instructors without their salary and find all instructors in the Biology department

```sql
CREATE VIEW faculty AS
    SELECT  ID,
            name,
            dept_name
    FROM    instructor;

SELECT  name
FROM    faculty
-- Views can be used like relations
WHERE   dept_name = 'Biology';
```

* e.g., A view of department salary totals

```sql
CREATE VIEW 
    dept_total_salary (dept_name, total_salary) AS
        SELECT  dept_name, SUM(salary)
        FROM    instructor
        GROUP BY dept_name;
```

<h3>Views Expansion (View Defined Using Other Views)</h3>

A view can be used in the expression defining another view.

* <strong>Direct dependency</strong><br>
A view relation $v_1$​ is said to depend directly on a view relation $v_2$​, if $v_2$​ is used in the expression defining $v_1$​.

* <strong>Dependency</strong><br>
A view relation $v_1$​ is said to depend on a view relation $v_2$​, if either $v_1$​ depends directly to $v_2$​, or there exists a path of dependencies from $v_1$​ to $v_2$​.

* <strong>Recursion</strong><br>
A view relation $v$ is said to be recursive if it depends on itself.

And <strong>view expansion</strong> is a way to define the meaning of views defined in terms of other views. Suppose that a view $v_1$ is defined by an expression $e_1$​ that may itself contain uses of view relations. It follows the following replacement step:

```
REPEAT
    Find any view relation v_i in e_1
    Replace the view relation v_i by the expression defining v_i
UNTIL nomore view relation are present in e_1
```

As long as the view definitions are not recursive, the loop will terminate eventually.

<br>

<strong>Example</strong>

```sql
CREATE VIEW physics_fall_2017 AS
   	SELECT course.course_id, sec_id, building, room_number
   	FROM course, section
   	WHERE course.course_id = section.course_id
                      AND course.dept_name = 'Physics'
                      AND section.semester = 'Fall'
                      AND section.year = 2017;
CREATE VIEW physics_fall_2017_watson AS
    	SELECT course_id, room_number
    	FROM physics_fall_2017
    	WHERE building= 'Watson';
```

## Materialized Views

Certain database systems allow view relations to be physically stored, that is, a physical copy of the ‘virtual’ relation created when the view is defined. Such views are called <strong>materialized views</strong>. It is especially efficient for views that are very commonly used.

If relations used in the query are updated, the materialised view reult becomes <strong>out-of-date</strong>; views must be maintained to date by updating the view whenever the underlying relations are updated.

<table style="width:60%;border-collapse:collapse;border-spacing:0;margin-left:auto;margin-right:auto; auto" class="center"><thead><tr><th style="border-color:inherit;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;font-weight:normal;overflow:hidden;padding:10px 5px;text-align:center;vertical-align:top;word-break:normal"></th><th style="border-color:inherit;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;font-weight:bold;overflow:hidden;padding:10px 5px;text-align:center;vertical-align:top;word-break:normal">Virtual Relation</th><th style="border-color:inherit;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;font-weight:bold;overflow:hidden;padding:10px 5px;text-align:center;vertical-align:top;word-break:normal">Materialization</th></tr></thead><tbody><tr><td style="border-color:inherit;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;font-weight:bold;overflow:hidden;padding:10px 5px;text-align:center;vertical-align:top;word-break:normal">Adv.</td><td style="border-color:inherit;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:center;vertical-align:top;word-break:normal">No updated required</td><td style="border-color:inherit;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:center;vertical-align:top;word-break:normal">No query rewriting required</td></tr><tr><td style="border-color:inherit;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;font-weight:bold;overflow:hidden;padding:10px 5px;text-align:center;vertical-align:top;word-break:normal">Disadv.</td><td style="border-color:inherit;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:center;vertical-align:top;word-break:normal">Queries must be rewrited every time</td><td style="border-color:inherit;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:center;vertical-align:top;word-break:normal">Update required whenever the base relations are updated</td></tr></tbody></table><br>

For example, consider the view <code>departments_total_salary</code>. If that view is materialized, its results would be stored in the database, allowing queries that use the view to potentially run much faster by using the precomputed view result, instead of recomputing it.

However, if an <code>instructor</code> tuple is added to or deleted from the <code>instructor</code> relation, the result of the query defining the view would change, and as a result the materialized view’s contents must be updated. Similarly, if an instructor’s salary is updated, the tuple in <code>departments_total_salary</code> corresponding to that instructor’s department must be updated.

<br>

## Update of View 

Although views are a useful tool for queries, they present serious problems when undergoing updates, insertions, or deletions.

<h3>- Missing Attribute</h3>

* e.g., Add a new tuple to the view faculty defined earlier

    ```sql
    INSERT INTO faculty VALUES
        ('30765', 'Green', 'Music');
    ```

* This insertion must be represented by the insertion into the instructor relation, which must have a salary attribute that is inaccessible. There are two reasonable approaches to dealing with this insertion:
    * Reject the insertion.
    * Insert a tuple with the value of the non-existing attribute set to <code>NULL</code>.
        * But will be rejected by the DBMS if that attribute has <code>NOT NULL</code> constraint.

<h3>- Ambiguity</h3>

Another issue with modification of the database through views is that <strong>some updates cannot be translated uniquely</strong>.

* <strong>Example</strong>

    ```sql
    CREATE VIEW instructor_info AS
        SELECT  ID, name, building
        FROM    instructor, department
        WHERE   instructor.dept_name = department.dept_name
    INSERT INTO instructor_info 
        VALUES ('69987', 'White', 'Taylor');
    ```

    * <strong>Issues</strong>
        * Which department, if multiple departments in Taylor?
        * What if no department is in Taylor?
    * There is no way to update the relations <code>instructor</code> and <code>department</code> by using <code>NULL</code> values to get the desired update on the view <code>instructor_info</code>.

* <strong>Example</strong>

    ```sql
    CREATE VIEW history_instructors AS
        SELECT  *
        FROM    instructor
        WHERE   dept_name = 'History'
    INSERT INTO history_instructors 
        VALUES ('25566', 'Brown', 'Biology', 7000);
    ```

    * <strong>Issues</strong>
        * Should the insertion be allowed? 
            * This tuple <em>can</em> be inserted into the <code>instructor</code> relation following the rules below, but it would not appear in the <code>history_instructors</code> view since it does not satisfy the selection imposed by the view.

Because of problems such as these, modifications are generally not permitted on view relations, except in limited cases. Different database systems specify different conditions under which they permit updates on view relations. 

And in general, most SQL implementations allow updates only on simple views:
* The <code>FROM</code> clause has only one database relation.
* The <code>SELECT</code> clause contains only attributes of the relation, and does not have any expression, aggregates, or DISTINCT specification.
* Any attribute not listed in the <code>SELECT</code> clause can be set to <code>NULL</code>.
* The query does not have a <code>GROUP BY</code> or <code>HAVING</code> clause.

<br>

# Transactions

* A <strong>transaction</strong> consists of a sequence of query and/or update statements. It is a <strong>unit of work</strong>.
* The SQL standard specifies that a transaction begins implicitly when an SQL statement is executed.
* The transaction must end with one of the following statements:
    * <strong>Commit work</strong>
    The updates performed by the transaction become permanent in the database; cannot be undone by rollback work;
    * <strong>Rollback work</strong>
    All the updates performed by the SQL statements in the transaction are undone;
* The database provides an abstraction of a transaction as being <strong>atomic</strong>, that is, indivisible. Either all the effects of the transaction are reflected in the database, or none are (after rollback).

<br>

# Integrity Constraints

<strong>Integrity constraints</strong> ensure that changes made to the database by authorised users do not result in a loss of data consistency. Thus, integrity constraints guard against accidental damage to the database.

There are some integrity-constraint statements allowed to be included in the <code>CREATE TABLE</code> command:

* <code>NOT NULL</code>
* <code>PRIMARY KEY</code>
* <code>UNIQUE</code>
* <code>CHECK(&lt;predicate&gt;)</code>

## <code>NOT NULL</code> constraint

* The <code>NULL</code> value is a member of all domains, hence is a legal value for every attributie in SQL by default.
* However, for certain attributes, <code>NULL</code> values may be inappropriate. 
* The <code>NOT NULL</code> specification is an example of <strong>domain constraint</strong> that prohibits the insertion of a <code>NULL</code> value for the attribute.

    ```sql
    A D NOT NULL
    ```

    * <code>A</code>: Attribute
    * <code>D</code>: Domain

## <code>UNIQUE</code> constraint

* THe <code>UNIQUE</code> specification states that the attributes specified form a candidate key.

    ```sql
    UNIQUE (A_1, ..., A_n)
    ```

    * <code>A_i</code>: Attributes

* Attributes $(A_1, \cdots, A_n)$ form a candidate key; that is, no two tuples can be equal on all the listed attributes.
* Candidates keys are permitted to be <code>NULL</code> (unless explicitly declared to be <code>NOT NULL</code>), in contrast to primary keys.

## <code>CHECK</code> clause

* The <code>CHECK</code> clause specifies a predicate <code>P</code> that must be satisfied by every tuple in a relation. 
    * e.g., Ensure that <code>semester</code> is one of 'Autumn', 'Winter', 'Spring', or 'Summer'

        ```sql
        CREATE TABLE section
        (
            ...
            semester    VARCHAR(6),
            ...
            CHECK(semester IN ('Autumn', 'Winter', 'Spring', 'Summer'))
        )
        ```
* The predicate in the <code>CHECK</code> clause can be an arbitrary predicate that can include a subquery (but jot supported in SQLite3). 

    ```sql
    CHECK
    (
        time_slot_id IN
        (
            SELECT  time_slot_id
            FROM    time_slot
        )
    )
    ```

    * The condition must be checked not only when a tuple is inserted or modified in <code>section</code>, but also when the relation <code>time_slot</code> changes.

* <code>CHECK</code> clause is satisfied if it is not false, so clauses that evalutate to <code>UNKNOWN</code> are not violations. 

## Referential Integrity

* <strong>Referential integrity</strong> ensures that a value that appears in one relation (<em>referencing</em> relation) for a given set of attributes also appears for a certain set of attributes in another relation (<em>referenced</em> relation).
* By default, a foreign key references the primary key attributes of the referenced table. Non-primary keys can also be referenced by foreign keys, but the attribute must have <code>UNIQUE</code> constraint specified.
* SQL allows a list of attributes of the referenced relation to be specified explicitly:

    ```sql
    FOREIGN KEY(A_1, ... , A_k) REFERENCES r(B_1, ... , B_k)
    ```

    * <code>A_i</code>, <code>B_i</code>: Attributes
    * <code>r</code>: Reference relation

### Cascading Actions

* When a referential integrity constraint is violated, the normal procedure is to reject the action that caused the violation. An alternative in case of <code>DELETE</code> or <Code>UPDATE</code> is to cascade actions.
* <code>CASCADE</code> specification ensures that whenever a referenced key is deleted or updated (depending on the specification), the same operation is cascaded to all the referencing keys.

    ```sql
    CREATE TABLE course
    (
        ...
        dept_name   VARCHAR(20),
        FOREIGN KEY (dept_name) REFERENCES department
            ON DELETE CASCADE
            ON UPDATE CASCADE
    );
    ```
    * If a <code>DELETE</code> of a tuple in <code>department</code> results in this reference integrity constraint being violated, the <code>DELETE</codE> operation <strong>cascades</strong> to the <code>course</code> relation, deleting the tuple that refers to the department that was deleted. Similar process also happens for <code>UPDATE</code>.

* The followings can be used instead of <code>CASCADE</code>:
    * <strong><code>SET NULL</code></strong><br>
    Whenever a tuple of referenced key relation is removed, set the value of <strong>all</strong> tuples of the referencing key with the same value to <code>NULL</code>.

    * <strong><code>SET DEFAULT</code></strong><br>
    Same as above, but use default value instead of <code>NULL</code>.

    * <strong><code>NO ACTION</code></strong><br>
    No action is done; referential integrity is violated.

    * <strong><code>RESTRICT</code></strong><br>
    An error is returne.

## Integrity Constraint Violation During a Transaction

* Integrity constraints may be violated temporarily during transactions.
    * e.g., John and Mary are married to each other by inserting two tuples, one for John and one for Mary, in the preceding relation, with the spouse attributes set to Mary and John, respectively. But, the insertion of the first tuple would violate the foreign-key constrain

        ```sql
        CREATE TABLE person
        (
            ID      CHAR(10),
            name    VARCHAR(40),
            spouse  CHAR(10),
            PRIMARY KEY (ID),
            FOREIGN KEY (spouse) REFERENCES person
        );
        ```

How can a tuple be inserted without causing constraint violation? There are some ways to do this:

* Insert <code>father</code> and <code>mother</code> of a person before inserting that person
* Set <code>father</code> and <code>mother</code> to <code>NULL</code> initially, and update after inserting all persons
    * Not possible if father and mother attributes are declared to be <code>NOT NULL</code>
* Defer constraint checking until the end of the transaction
    * <code>INITIALLY DEFERRED</code> clause


## Assertions

* An <strong>assertion</strong> is a predicate expressing a condition that we wish the database always to satisfy
* An assertion in SQL takes the form:
    * <code>create assertion &lt;assertion-name&gt; check (&lt;predicate&gt;)</code>;
* Example
    * For each tuple in the student relation, the value of the tot_cred attribute must equal to the sum of credits of the courses that the student has completed successfully

        ```sql
        CREATE ASSERTION credits_earned_constraint CHECK 
        (NOT EXISTS (SELECT ID 
                     FROM student
                     WHERE tot_cred <> (SELECT COALESCE (SUM (credits), 0)
                                        FROM takes NATURAL JOIN course
                                        WHERE student.ID = takes.ID
                                            AND grade IS NOT NULL and grade <> 'F')))
        ```

    * An instructor cannot teach in two different classrooms in a semester in the same time slot
* Not supported in SQLite3

<br>

# SQL Data Types and Schemas

In addition to the basic data types, the SQL standard supports several data types.

## Built-in Data Types in SQL

* <code>DATE</code>
    * Dates in <code>YYYY-MM-DD</code> format
    * e.g. <code>DATE '2005-07-27'</code>
* <code>TIME</code>
    * Time of day in hours, minutes, and seconds
    * e.g. <code>TIME '09:00:30'</code>, <code>TIME '09:00:30.75'</code>
* <code>TIMESTAMP</code>
    * DATE + TIME
    * e.g. <code>TIMESTAMP '2001-11-15 09:00:30.75'</code>

SQL alloows comparison operations on all the types listed above. SQL also allows computations based on dates and times and on intervals:

* <code>INTERVAL</code>
    * <code>INTERVAL &lt;value&gt; &lt;unit&gt;</code>
    * e.g. <code>INTERVAL '1' day</code>
    * Subtracting a <code>DATE</code> / <code>TIME</code> / <code>TIMESTAMP</code> from another gives an <code>INTERVAL</code> value

## <code>CAST</code>, <code>COALESCE</code>, <code>DEFAULT</code>

* We can use an expression of the following form to convert an expression <code>e</code> to the type <code>t</code>:

    ```sql
    CAST (e AS t)
    ```
* We can choose how <code>NULL</code> values are output in a query result using the <codE>COALESCE</code> function:

    ```sql
    SELECT ID, COALESCE (salary, 0) AS salary
    FROM instructor
    ```

* SQL allows a <code>DEFAULT</code> value to be specified for an attribute:

    ```sql
    CREATE TABLE STUDENT
        (ID     VARCHAR (5),
         name   VARCHAR (20) NOT NULL,
         dept_name  VARCHAR (20), 
         tot_cred   NUMERIC (3,0) DEFAULT 0,
         PRIMARY KEY (ID));
    ```

## Large-Object Types

* Many current-generation database applications need to store attributes that can be large.
* Large objects (photos, videos, CAD files, etc.) are stored as a large object:
    * <code>BLOB</code>: <strong>binary large object</strong>
        * an object is a large collection of uninterpreted binary data (whose interpretation is left to an application outside of the database system)
    * <code>CLOB</code>: <strong>character large object</strong>
        * an object is a large collection of character data
* When a query returns a large object, a <strong>pointer</strong> is returned rather than the large object itself

## User-Defined Types

* SQL provides the notion of distinct types. The <code>CREATE TYPE</code> clause can be used to define new types.

    ```sql
    CREATE TYPE new_t AS pre_t FINAL;
    ```
    * <code>new_t</code>: Name of the new type
    * <code>pre_t</code>: Pre-existing type
    * Use the <code>[NOT] FINAL</code> clause to indicate whether any further subtypes can be created for this type.
        * (Default) Specify <code>FINAL</code> if no further subtypes can be created for this type.
        * Specify <code>NOT FINAL</code> if further subtypes can be created under this type.
    * e.g. comparing the <code>Dollar</code> value and <code>Pound</code> value is not valid

        ```sql
        CREATE TYPE Dollars AS NUMERIC (12, 2) FINAL;
        CREATE TYPE Pounds AS NUMERIC (12, 2) FINAL;
        ```
    
    * Then, an attempt to assign a value of type <code>Dollars</code> to a variable of type <code>Pounds</code> results in a compile-time error
* SQL provides <code>DROP TYPE</code> and <code>ALTER TYPE</code> clauses to drop or modify types that have been created earlier.
* Before user-defined types were added to SQL, SQL had similar but subtly different notion of domain, which can add <strong>integrity constraints</strong> to an underlying type. (Not supported in SQLite3)

    ```sql
    CREATE DOMAIN new_d pre_t
        CONSTRAINT ...
    ```

    * <code>new_d</code>: Name of the new domain
    * <code>pre_t</code>: Pre-existing type
    * e.g.
    
        ```sql
        CREATE DOMAIN Dollars AS NUMERIC (12, 2) NOT NULL;
        ```
    * e.g.

        ```sql
        CREATE DOMAIN YearlySalary NUMERIC (8, 2)
            CONSTRAINT salary_value_test CHECK (VALUE >= 29000.00);
        ```

* There are two significant differences between types and domains:
    * Domains can have constraints specified on them, and can have default values defined for variables of the domain type, whereas user defined types cannot have constraints of default values specified on them.
    * Domains are not strongly typed; values of one domain can be assigned to values of another domain as long as the underlying types are compatible.

<br>

# Index Creation

* Many queries reference only a small proportion of the records in a table. Thus, it is inefficient for the system to read every record to find a record with particular value.
* An <strong>index</strong> on an attribute of a relation is a data structure that allows the database system to find those tuples in the relation that have a specified value for that efficiently, without scanning through all the tuples of the relation.

    ```sql
    CREATE INDEX i ON r(A_1, ..., A_k)
    ```

    * <code>i</code>: Name of the index
    * <code>r</code>: Relation
    * <code>A_i</code>: Attributes

* For example, 

    ```sql
    CREATE INDEX studentID_idx ON student(ID);
    
    SELECT  *
    FROM    student
    WHERE   ID = '12345'
    ```

    * The above query can be executed by using the index to find the required record, without looking at all records of student.

<br>

# Authorization

A user can be assigned several forms of authorisations on parts of the database:

* <strong>Read</strong>: Allows reading, but not modification of data
* <strong>Insert</strong>: Allows insertion of new data, but not modification of existing data
* <strong>Update</strong>: Allows modification, but not deletion of data
* <strong>Delete</strong>: Allows deletion of data

Each of these types of authorisations is called <strong>privilege</strong>.

A user can be authorised all, none, or a combination of these types of privileges on specified parts of a database, such as a relation or a view.

## <code>GRANT</code> statement

* The <code>GRANT</code> statement is used to <strong>confer</strong> authorization.

    ```sql
    GRANT <privilege list>
    ON <reltation/view>
    TO <user/role list>;
    ```

* Here, <code>&lt;privilege list&gt;</code> can be
    * <code>SELECT</code>: Allows read access, or the ability query using the view
    * <code>INSERT</code>: Ability to insert tuples
    * <code>UPDATE</code>: Ability to update using the SQL <code>UPDATE</code> statement
    * <code>DELETE</code>: Ability to delete tuples
    * <code>ALL PRIVILEGES</code>: Short form for all the allowable privileges

* The <code>&lt;user list&gt;</code> can be
    * user ID
    * <code>PUBLIC</code>, which allows all valid users the privilege granted
    * <strong>role</strong> (we cover this concept in the next section)
* For example,

    ```sql
    GRANT SELECT ON department TO Amit, Satoshi
    GRANT UPDATE (budget) ON department TO Amit, Satoshi
    ```

* Note that 
    * Granting a privilege on a view does <strong>not</strong> imply granting any privileges on the underlying relations. 
    * The grantor of the privilege <strong>must</strong> already hold the privilege on the specified item.
 
## <code>REVOKE</code> statement

* The <code>REVOKE</code> statement is used to <strong>revoke</strong> authorization.

    ```sql
    REVOKE <privilege list>
    ON <reltation / view>
    TO <user list>;
    ```
    * the <code>&lt;privilege list&gt;</code> may be <code>ALL</code>: <strong>ALL</strong> privileges the revokee may hold, are revoked.
    * If the <code>&lt;user list&gt;</code> is <code>PUBLIC</code>: <strong>ALL</strong> users lose the privilege, except those granted it explicitly. 
* For example,

    ```sql
    REVOKE SELECT ON department TO Amit, Satoshi
    REVOKE UPDATE (budget) ON department TO Amit, Satoshi
    ```

* Note that 
    * If the same privilege was granted more than once to the same user by <strong>different</strong> grantors, then the user may <strong>retain</strong> the privilege after one revocation.
    * All privileges that depend on the privilege being revoked are <strong>also revoked</strong>.
        * e.g., in some DBMSs (not all DBMSs), the select privilege is required for the insert, update, and delete privileges

## Roles

* A <strong>role</strong> is a way to distinguish among various users as far as what these users can access / update in the database.
    * e.g., each instructor must have the same types of authorizations on the same set of relations. Whenever a new instructor is appointed, she will have to be given all these authorizations individually.
* A role can be thought of as a set of <em>privileges</em>; with roles, privileges of multiple users can be managed at once.
* Roles can be created as following:

    ```sql
    CREATE ROLE <name>
    ```

    * Once a role is created, users can be assigned to the role as following.

        ```sql
        GRANT   <role>
        TO      <user list>
        ```

* For example,

    ```sql
    CREATE ROLE instructor
    
    GRANT SELECT ON takes
    TO instructor

    CREATE ROLE dean
    GRANT instructor TO dean;
    GRANT dean TO Satoshi;
    ```

* Note that 
    * Privileges can be granted to roles.
    * Roles can be granted to users, as well as to other roles.
    * Thus, there can be a chain of roles
        * e.g. <code>teaching_assistant</code> $\to$ <code>instructor</code> $\to$ <code>dean</code> 


## Authorization on Views

* A user who creates a <strong>view</strong> must have at least <code>SELECT</code> privilege on the base relation.
* The creator of the view does not receive all privileges on that view.
    * Obviously, only those privileges that provide no additional authorization beyond those that the user already had, are given.
    * e.g., a user who creates a view can't be given <code>UPDATE</code> authorization on a view without having <code>UPDATE</code> authorization on the relations used to define the view.
* Users who received only the privilege on the view does not have privilege on the base relation.
* For example

    ```sql
    /* The creator of a view should have the select privilege on the base relation */
    CREATE VIEW geo_instructor AS
        (SELECT *
        FROM instructor
        WHERE dept_name = 'Geology');
    GRANT SELECT ON geo_instructor TO geo_staff;

    /* The staff member (geo_staff) is authorized to see the result of this query */
    SELECT *
    FROM geo_instructor;
    ```

## <code>REFERENCES</code> Privilege

* SQL includes a <code>REFERENCES</code> privilege that permits a user to declare foreign keys when creating relations.

    ```sql
    GRANT   REFERENCES(A_1, ... , A_k)
    ON      r
    TO      <user list>;
    ```

    * <code>r</code>: Relation
    * <code>A_i</code>: Attributes of <code>r</code>

* Such privilege is required because
    * Foreign key constraints restrict deletion and update operations on the referenced relation, which may restrict future activity by other users.
    * With the <code>REFERENCES</code> privilege, the user can <strong>check for the existence</strong> of a certain value in a certain (set of) attributes of the referenced relation.


## Transfer of Privileges

* By default, a user/role that is granted a privilege is not authorized to grant that privilege to another user/role
* Add <strong><code>WITH GRANT OPTION</code></strong> clause to grant the privilege to allow the recipient to <strong>pass the privilege</strong> onto other users can be explicitly given.

    ```sql
    GRANT   <privilege list>
    ON      <relation / view>
    TO      <user list>
    WITH GRANT OPTION;
    ```
* The passing of a specific authorization from one user to another can be represented by an <strong>authorization graph</strong>.
    * A user has an authorization <em>if and only if</em> there is a path from the root of the authorization graph down to the node representing the user.

<p align = "center">
<img width="250" alt="image" src="/assets/images/database-system/intermediate/authorization-graph.png">
<figcaption style="text-align: center;">$\mathbf{Fig\ 4.}$ IAuthorization-grant graph ($U_1$, $U_2$, $\cdots$, $U_5$ are users and $\textrm{DBA}$ refers to the database administrator) </figcaption>
</p><br>

* A pair of devious users might attempt to defeat the rules for revocation of authorization by granting authorization to each other.
    * e.g. $U_2$ grants his/her privileges to $U_3$. When DBA revokes $U_2$'s grant, $U_2$ might retains authorization $U_3$.
    * Thus, SQL ensures that the authorization is revoked from both the users.
* Revocation of a privilege from a user/role may cause other users/roles also to lose that privilege
    * Cascading revocation is also inappropriate in many situations. 
    * e.g. Satoshi has the role of <code>dean</code>, grants <code>instructor</code> to Amit. When the role <code>dean</code> is revoked from Satoshi later; Amit continues to be employed on the faculty and should retain the <code>instructor</code> role.
* It is also possible to specify the actions when a privilege is revoked from a user.
    * One option is to <strong>cascade</strong> the revoking operation: all privileges the revokee granted with <code>GRANT OPTION</code> is also revoked.

        ```sql
        REVOKE  <privilege list>
        ON      <relation / view>
        FROM    <user list>
        CASCADE;
        ```

        * The keyword <code>CASCADE</code> can be omitted, as it is the basic behavior of the <code>REVOKE</code> operation. 

    * Another option is to <strong>restrict</strong> cascading revocation; maintain the privileges the revokee granted to other users, and only revoke the revokee’s privilege.

        ```sql
        REVOKE  <privilege list>
        ON      <relation / view>
        FROM    <user list>
        RESTRICT;
        ```

<br><br><br>

# Reference
**[1]** [Silberschatz, Abraham, Henry F. Korth, and Shashank Sudarshan. "Database system concepts." (2011).](https://db-book.com/) <br>