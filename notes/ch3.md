---
title:  "[DB] Introduction to SQL" 
excerpt: ""
categories:
  - Database System
tags:
  - [Computer Science, DB]
header:
  teaser: "/assets/images/database-system/teaser/sql.png"

toc: true
toc_sticky: true

date: 2024-03-11
last_modified_at: 2024-03-11

lang: en
ref: introSQL
use_math: true
---

# Overview of SQL

The SQL language has several parts:

<ul>
    <li><strong>Data-Definition Language (DDL)</strong><br>
        Provides commands for defining relation schemas, deleting relations, and modifying relation schemas.
    </li>
    <li><strong>Data-Manipulation Language (DML)</strong><br>
        Provides the ability to query information from the database and to insert tuples into, delete tuples from, and modify tuples in the database.
    </li>
    <li><strong>Integrity</strong><br>
        Provides commands for specifying integrity constraints that the data stored in the database must satisfy.
    </li>
    <li><strong>View Definition</strong><br>
        Provides commands for defining views.
    </li>
    <li><strong>Transaction Control</strong><br>
        Provides commands for specifying the beginning and ending of transactions.
    </li>
    <li><strong>Embedding SQL & Dynamic SQL</strong><br>
        Define how SQL statements can be embedded within general-purpose programming languages.
    </li>
    <li><strong>Authorization</strong><br>
        Provides commands for specifying access rights to relations and views.
    </li>
</ul><br>

# SQL Data Definition

## Basic Types

The SQL standard supports a variety of built-in types, including:

<ul>
    <li><strong><code>CHAR(n)</code></strong><br>
        Fixed-length character string, with user-specified length $n$
    </li>
    <li><strong><code>VARCHAR(n)</code></strong><br>
        Variable-length character string, with user-specified maximum length $n$
    </li>
    <li><strong><code>INT</code></strong><br>
        Integer (machine-dependent)
    </li>
    <li><strong><code>SMALLINT</code></strong><br>
        Small integer (machine-dependent)
    </li>
    <li><strong><code>NUMERIC(p, d)</code></strong><br>
        Fixed-point number, with user-specified precision of $p$ digits (plus a sign), with $d$ digits to the right of the decimal point <code>NUMERIC(3, 1)</code> allows 44.5 to be stored exactly, but neither 444.5 or 0.32 can be stored exactly.
    </li>
    <li><strong><code>REAL, DOUBLE PRECISION</code></strong><br>
        Floating-point and double precision floating-point numbers (machine-dependent precision)
    </li>
    <li><strong><code>FLOAT(n)</code></strong><br>
        Floating-point number, with user-specified precision of at least $n$ digits
    </li>
</ul>

Each type may include a special value called the <strong><code>NULL</code> value</strong>. This indicates an absent value that may exist but be unknown or that may not exist at all. 

<br>

## Basic schema definition

An SQL relation is defined using the <code>CREATE TABLE</code> command with the following general form:


```sql
CREATE TABLE r
(
	A_1 D_1,
	... ,
	A_n D_n,
	[Integrity Constraint 1],
	... ,
	[Integrity Constraint k]
)
```

* <code>r</code>: Name of the relation
* <code>A_i</code>: Attribute name in the schema of the relation <code>r</code>
* <code>D_i</code>: Domain of attribute <code>A_i</code>; Type of attribute <code>A_i</code>
<br>

SQL prevents any update to the database that violates an <strong>integrity constraints</strong>. SQL supports a number of different <strong>integrity constraints</strong>:

<ul>
    <li><strong><code>PRIMARY KEY(A_1, ... , A_n)</code></strong>
        <ul>
            <li>Attributes <code>A_1, ... , A_n</code> form the primary key for the relation.</li>
            <li>Required to be <em>non-null</em> and <em>unique</em>.</li>
        </ul>
    </li>
    <li><strong><code>FOREIGN KEY(A_1, ... , A_n) REFERENCES s</code></strong><br>
        Values of attributes <code>A_1, ... , A_n</code> for any tuple in the relation must correspond to values of the primary key attributes (or any attributes with <code>UNIQUE</code> constraint specified) of some tuple in relation <code>s</code> 
        <br>
        <br>
        Some database systems, including MySQL, require an alternative syntax, <code>FOREIGN KEY(A_1, ... , A_n) REFERENCES s(B_1, ..., B_n)</code>, where the referenced attributes in the referenced table are listed explicitly.
    </li>
    <li><strong><code>NOT NULL</code></strong><br>
        <code>NULL</code> value is not allowed for that attribute
    </li>
</ul>

Here are some examples.

```sql
CREATE TABLE instructor
(
	ID           CHAR(5),
	name         VARCHAR(20) NOT NULL,
	dept_name    VARCHAR(20),
	salary       NUMERIC(8, 2),
	PRIMARY KEY(ID),
	FOREIGN KEY(dept_name) REFERENCES department
);
```

```sql
CREATE TABLE takes
(
	ID           VARCHAR(5),
	course_id    VARCHAR(8),
	sec_id       VARCHAR(8),
	semester     VARCHAR(6),
	year         NUMERIC(4,0),
	grade        VARCHAR(2), 
	PRIMARY KEY(ID, course_id, sec_id, semester, year),
	FOREIGN KEY(ID) REFERENCES student,
    FOREIGN KEY(course_id, sec_id, semester, year) REFERENCES section
);
```
<br>

A newly created relation is empty initially. Inserting tuples into a relation, updating them, and deleting them are possible by following commands:

<ul>
    <li><strong><code>INSERT INTO s t</code></strong>
        <ul>
            <li>Inserts tuple <code>t</code> into the relation <code>s</code></li>
            <li>e.g., <code>INSERT INTO instructor VALUES('10211', 'Smith', 'Biology', 66000)</code></li>
        </ul>
    </li>
    <li><strong><code>DELETE FROM s t</code></strong>
        <ul>
            <li>Deletes tuple <code>t</code> from the relation r</li>
            <li><code>DELETE FROM r</code> deletes all tuples from the relation <code>r</code></li>
        </ul>
    </li>
    <li><strong><code>DROP TABLE r</code></strong>
        <ul>
            <li>Deletes all information of <code>r</code>, including the table itself, from the database</li>
            <li>After <code>r</code> is dropped, no tuples can be inserted into it unless it is re-created with the <code>CREATE TABLE</code> command</li>
            <li>e.g., <code>DROP TABLE student</code></li>
        </ul>
    </li>
    <li><strong><code>ALTER TABLE</code></strong>
        <ul>
            <li><strong><code>ALTER TABLE r ADD A D</code></strong>
                <ul>
                    <li><code>A</code> is the name of the attribute to be added to relation <code>r</code>, and <code>D</code> is the domain of <code>A</code></li>
                    <li>All existing tuples in the relation are assigned NULL as the value for the new attribute</li>
                </ul>
            </li>
            <li><strong><code>ALTER TABLE r DROP A</code></strong>
                <ul>
                    <li><code>A</code> is the name of an attribute of relation <code>r</code></li>
                    <li>Drop attributes from a relation</li>
                </ul>
            </li>
        </ul>
    </li>
</ul><br>

# Basic Structure of SQL Queries 

A typical SQL query consists of three clauses: <code>SELECT</code>, <code>FROM</code>, and <code>WHERE</code>

```sql
SELECT  A_1, ... , A_n
FROM    r_1, ... , r_m
WHERE   p
```

* <code>A_i</code>: Attributes
* <code>r_i</code>: Relations
* <code>p</code>: Predicate

Note that the result of an SQL query is a relation.

## <code>SELECT</code> Clause

* <strong><code>SELECT</code> clause</strong> lists the attributes desired in the result of a query.
    * Corresponding to the projection operation $\Pi$ of the relational algebra
    * e.g., 
    
        ```sql
        SELECT name 
        FROM instructor
        ```
    
* <strong><code>DISTINCT</code> keyword</strong> inserted after <code>SELECT</code> eliminates all duplicates.
    * In practice, duplicate elimination is time-consuming. Therefore, SQL allows duplicates in database relations as well as in the results of SQL expressions. Thus, the preceding SQL query lists each department name once for every tuple in which it appears in the instructor relation.
    * e.g.,
        
        ```sql
        SELECT DISTINCT dept_name
        FROM instructor
        ```
* <strong><code>ALL</code> keyword</strong>, in contrast, specifies explicitly that duplicates should not be removed, although it is the default of <code>SELECT</code> clause.
    * In practice, duplicate elimination is time-consuming. Therefore, SQL allows duplicates in database relations as well as in the results of SQL expressions.Thus, the preceding SQL query lists each depart- ment name once for every tuple in which it appears in the instructor relation.
    * e.g., 
    
        ```sql
        SELECT ALL dept_name
        FROM instructor
        ```

Few remarks:

* $*$ in the <code>SELECT</code> clause denotes <em>all attributes</em>;
    * e.g., 
    
        ```sql
        SELECT * 
        FROM instructor
        ```
    * e.g., 
    
        ```sql
        SELECT instructor.* 
        FROM instructor, teaches
        WHERE instructor.ID = teaches.ID;
        ```

* An attribute can be a literal without <code>FROM</code> clause;
    * e.g., 
    
        ```sql
        SELECT '437'
        ```

    * The result is a table with one column and a single row with value "437"
    * A column name can be given using:  

        ```sql
        SELECT '437' AS foo
        ```

* An attribute can be a literal with <code>FROM</code> clause;
    * e.g., 
    
        ```sql
        SELECT 'A' FROM instructor
        ```
        
    * The result is a table with one column and $N$ rows (the number of tuples in the instructor table), each row with value "A"
* <code>SELECT</code> clause can contain arithmetic expressions $+$, $−$, $*$, and $/$ operating on constants or attributes of tuples
    * e.g., ```SELECT ID, name, dept name, salary * 1.1 FROM instructor;```
    * Returning a relation that is the same as the instructor relation, except that the value of the salary attribute is multiplied by 1.1
    * Being possible to rename “salary/12” using the as clause

        ```sql
        SELECT ID, name, salary/12 AS monthly_salary
	    FROM instructor
        ```

## <code>FROM</code> Clause

* <strong><code>FROM</code> clause</strong> lists the relations involved in the query.
    * Corresponding to the Cartesian product $\times$ of the relational algebra
    * e.g., find the Cartesian product $\textrm{instructor} \times \textrm{teaches}$

        ```sql
        SELECT *
        FROM instructor, teaches
        ```
    * the Cartesian product is useful combined with <code>WHERE</code> clause condition (selection operation $\sigma$ in the relational algebra)

## <code>WHERE</code> Clause

* <strong><code>WHERE</code> clause</strong> specifies conditions that the resulting relation must satisfy.
    * Corresponding to the selection operation $\sigma$ of the relational algebra
    * e.g., find all instructors in Comp. Sci. dept

        ```sql
        SELECT name
        FROM instructor
        WHERE dept_name = 'Comp. Sci.'
        ```
    * the Cartesian product is useful combined with <code>WHERE</code> clause condition (selection operation $\sigma$ in the relational algebra)
* SQL allows the use of the logical connectives <code>AND</code>, <code>OR</code>, and <code>NOT</code>.
    * The operands can be expressions involving the comparison operators $<$, $\leq$, $>$, $\geq$, $=$, and $<>$ ($\neq$)
* Comparisons can be applied to results of arithmetic expressions.

<br><br>

# Additional Basic Operations

A number of additional basic operations are supported in SQL.

## Rename operation

* SQL allows renaming relations and attributes using the <strong>AS clause</strong>, taking the form:

    ```sql
    old_name AS new_name
    ```

    * e.g., find the names of all instructors who earn a higher salary than some instructor in 'Comp. Sci'

    ```sql
    SELECT T.name
    FROM instructor AS T, instructor AS S
    WHERE T.salary > S.salary AND S.dept_name = 'Comp. Sci.'
    ```

    * The keyword <code>AS</code> is optional and may be omitted
        
    ```sql
    instructor AS T ≡ instructor T
    ```

    * Identifier, such as <code>T</code> and <code>S</code>, that is used to rename a relation is referred to as a <strong>correlation name</strong> in the SQL standard, or <strong>table alias</strong>, <strong>correlation variable</strong>, or <strong>tuple variable</strong>

<br>

## String operation

* SQL includes a string-matching operator for comparisons on character strings, using the operator <code>Like</code>:

    ```sql
    LIKE some_string
    ```

* It uses patterns; they can be described using two special characters: <code>%</code> and <code>_</code>.
    * <code>%</code>: Matches any string.
    * <code>_</code>: Matches any character.
    * e.g., 
        * <code>'Intro%'</code> matches any string beginning with 'Intro';
        * <code>'%Comp%'</code> matches any string containing 'Comp' as a substring;
        * <code>'_ _ _'</code> matches any string of exactly three characters;
        * <code>'_ _ _ %'</code> matches any string of at least three characters
    * e.g., find the names of all instructors whose name includes the substring “dar”

        ```sql
        SELECT name
        FROM instructor
        WHERE name LIKE '%dar%' 
        ```
    * Patterns are case-sensitive; uppercase characters do not match lowercase characters, or vice versa.

* SQL supports a variety of string operations such as:
    * <code>str1 || str2</code>: Concatenate str1 and str2.
    * <code>UPPER(str), LOWER(str)</code>: Converting from lower to upper / upper to lower cases.
    * <code>LENGTH(str)</code>: Finding the length of str.
    * <code>SUBSTR(str, start, length)</code>: Extracting substring.


## Escape Character

* For patterns to include the special pattern characters (<code>%</code> and <code>_</code>), SQL allows the specification of an <strong>escape character</strong> defined using the <code>ESCAPE</code> keyword.
* Used immediately before a special pattern character to indicate that it is to be treated like a normal character.

    ```sql
    LIKE some_string ESCAPE escape_character
    ```

    * For example,
        * <code>LIKE 'ab\%cd%' ESCAPE '\'</code> matches all strings beginning with 'ab%cd'.
        * <code>LIKE 'ab\\cd%' ESCAPE '\'</code> matches all strings beginning with 'ab\cd'.

## Ordering the Display of Tuples

* The <strong><code>ORDER BY</code> clause</strong> causes the tuples in the results of a query to appear in sorted (ascending) order.

    ```sql
    ORDER BY some_attribute
    ```

    * e.g., list the names of all instructors in alphabetical order

    ```sql
    SELECT DISTINCT name
    FROM   instructor
    ORDER BY name
    ```

* We may specify <code>DESC</code> for descending order or <code>ASC</code> for ascending order; ascending order is the default setting.
    * e.g., 

        ```sql
        ORDER BY name DESC
        ```

* <code>ORDER BY</code> can sort on multiple attributes.
    * e.g.,

        ```sql
        SELECT * 
        FROM instructor 
        ORDER BY salary DESC, name ASC;
        ```
        
## <code>WHERE</code> Clause Predicates

* SQL includes a <strong><code>BETWEEN</code> comparison</strong> operator to simplify WHERE clauses.

    ```sql
    WHERE some_attribute BETWEEN start_val AND end_val
    ```
    
* SQL allows the use of the notation <code>(v_1, ... , v_n)</code> to denote a tuple of arity $n$ containing values <code>v_1</code>, ... , <code>v_n</code>.
* The comparison operator can be used on tuples, and the ordering is defined lexicographically.
    * e.g., <code>(a_1, a_2) <= (b_1, b_2)</code> is true if both <code>a_1 <= b_1</code> and <code>a_2 <= b_2</code>.


<br><br>

# Set Operations in SQL

The SQL operations union, intersect, and except operate on relations and correspond to the mathematical set operations $\cup$, $\cap$, and $−$. 

## <code>UNION</code> operation

* The SQL operation <code>UNION</code> corresponds to the mathematical operation $\cup$.

    ```sql
    R1 UNION R2
    ```

    * e.g., Find the courses that ran in Fall 2017 or in Spring 2018

        ```sql
        SELECT course_id FROM section WHERE semester = 'Fall' AND year = 2017
        UNION
        SELECT course_id FROM section WHERE semester = 'Spring' AND year = 2018
        ```

* The <code>UNION</code> operation automatically eliminates duplicates.
    * To retain duplicates, one must use <code>UNION ALL</code> operation instead.

## <code>INTERSECT</code> operation

* The SQL operation <code>INTERSECT</code> corresponds to the mathematical operation $\cap$.

    ```sql
    R1 INTERSECT R2
    ```

    * e.g., Find the courses that ran in Fall 2017 and in Spring 2018

        ```sql
        SELECT course_id FROM section WHERE semester = 'Fall' AND year = 2017
        INTERSECT
        SELECT course_id FROM section WHERE semester = 'Spring' AND year = 2018
        ```

* The <code>INTERSECT</code> operation automatically eliminates duplicates.
    * To retain duplicates, one must use <code>INTERSECT ALL</code> operation instead.

## <code>EXCEPT</code> operation

* The SQL operation <code>EXCEPT</code> corresponds to the mathematical operation $-$ (set-difference).
    * It outputs all tuples from <code>R1</code> that do not occur in <code>R2</code>.

        ```sql
        R1 EXCEPT R2
        ```

    * e.g., Find the courses that ran in Fall 2017 but not in Spring 2018

        ```sql
        SELECT course_id FROM section WHERE semester = 'Fall' AND year = 2017
        EXCEPT
        SELECT course_id FROM section WHERE semester = 'Spring' AND year = 2018
        ```

* The <code>EXCEPT</code> operation automatically eliminates duplicates.
    * To retain duplicates, one must use <code>EXCEPT ALL</code> operation instead.

<br><br>

# <code>NULL</code> Values in SQL

* <strong><code>NULL</code></strong> values signifies an unknown value or that a value does not exist.
    * <em>Arithmetic operations</em>
      * The result of any arithmetic expression involving <code>NULL</code> is <code>NULL</code>.
    * <em>Comparison operations</em>
      * SQL treats the result of any comparison involving a <code>NULL</code> value (other than <code>IS NULL</code> and <code>IS NOT NULL</code>) as <code>UNKNOWN</code>
      * e.g., <code>5 < NULL</code>, <code>NULL = NULL</code>
      * The predicate in a <code>WHERE</code> clause can involve Boolean operations such as <code>AND</code>, <code>OR</code>, and <code>NOT</code>.

        ```sql
        --- AND
            TRUE AND UNKNOWN = UNKNOWN
            FALSE AND UNKNOWN = FALSE
            UNKNOWN AND UNKNOWN = UNKNOWN
        --- OR
            UNKNOWN OR TRUE = TRUE
            UNKNOWN OR FALSE = UNKNOWN
            UNKNOWN OR UNKNOWN = UNKNOWN
        --- NOT
            NOT UNKNOWN = UNKNOWN
        ```

        The result of <code>WHERE</code> clause predicate is treated as <code>FALSE</code> if the value evaluates to <code>UNKNOWN</code>.

    * <code>IS NULL</code> and <code>IS NOT NULL</code>
        * The predicate <code>IS NULL</code> can be used to check for <code>NULL</code> values.
        * The predicate <code>IS NOT NULL</code> succeeds if the value on which it is applied is not <code>NULL</code>.

<br><br>

# Aggregate Functions

* <strong>Aggregate functions</strong> are functions that take a collection of values as input, and return a single value.
* SQL offers five aggregate functions:
    * Average: <code>AVG</code>
    * Minimum: <code>MIN</code>
    * Maximum: <code>MAX</code>
    * Total: <code>SUM</code>
    * Count: <code>COUNT</code>
* The input to <code>AVG</code> and <code>SUM</code> must be a collection of numbers.
* Examples:
    * e.g., Find the average salary of instructors in the Computer Science department 

        ```sql
        SELECT AVG (salary) AS avg_salary
        from instructor
        WHERE dept_name = 'Comp. Sci.'
        ```

    * e.g., Find the total number of instructors who teach a course in the Spring 2018 semester

        ```sql
        SELECT AVG (salary)
        from instructor
        WHERE dept_name = 'Comp. Sci.'
        ```

    * e.g., Find the total number of instructors (need duplicates elimination) who teach a course in the Spring 2018 semester

        ```sql
        SELECT COUNT (DISTINCT ID)
        FROM teaches
        WHERE semester = 'Spring' AND year = 2018
        ```
    
    * e.g., Find the number of tuples in the course relation
        
        ```sql
        SELECT COUNT (*)
        FROM course
        ```
        * SQL does not allow the use of <code>DISTINCT</code> with <code>COUNT (*)</code>.

## <code>GROUP BY</code> clause 

* <code>GROUP BY</code> clause uses the given attributes to form groups.

    ```sql
    GROUP BY some_attributes
    ```

    * e.g., Find the average salary of instructors in each department.

        ```sql
        SELECT   dept_name, AVG(salary) AS avg_salary
        FROM     instructor
        GROUP BY dept_name
        ```
    
* Attributes in <code>SELECT</code> statement that is outside of aggregate functions must appear in the <code>GROUP BY</code> clause; otherwise the query is treated as erroneous.
    * e.g., the following is an <u>erroneous</u> query

        ```sql
        SELECT   dept_name, ID, AVG(salary) AS avg_salary
        FROM     instructor
        GROUP BY dept_name
        ```

## <code>HAVING</code> clause 

* SQL applies predicated in the <code>HAVING</code> clause after the formation of groups, whereas predicates in the <code>WHERE</code> clause are applied before the group formation.

    ```sql
    GROUP BY some_attributes
    HAVING   predicate
    ```

* A typical query containing aggregation, <code>GROUP BY</code>, and/or <code>HAVING</code> clauses is defined by the following sequence of operations:

<ol>
    <li>The <code>FROM</code> clause is first evaluated to get a relation.</li>
    <li>If a <code>WHERE</code> clause is present, the predicate in the <code>WHERE</code> clause is applied on the result relation of the <code>FROM</code> clause.</li>
    <li>Tuples satisfying the <code>WHERE</code> predicate are then placed into groups by the <code>GROUP BY</code> clause (if present). Otherwise the entire set of tuples satisfying <code>WHERE</code> clause’s predicate is treated as one single group.</li>
    <li>The <code>HAVING</code> clause (if present) is applied to each group, the groups that do not satisfying the <code>HAVING</code> predicate are removed.</li>
    <li>The <code>SELECT</code> clause uses the remaining groups to generate tuples of the result of the query, applying the aggregate functions to get a single result tuple for each group.</li>
</ol>

* e.g., For each course section offered in 2017, find the average total credits (tot cred) of all students enrolled in the section, if the section has at least 2 students.

    ```sql
    SELECT course_id, semester, year, sec_id, AVG (tot_cred)
    FROM student, takes
    WHERE student.ID = takes.ID AND year = 2017
    GROUP BY course_id, semester, year, sec_id
    HAVING COUNT (ID) >= 2;
    ```

## Aggregation with <code>NULL</code>

* All aggregate functions except <code>COUNT</code> ignore <code>NULL</code> values in their input collection.
* The <code>COUNT</code> of an empty collection is defined to be $0$.
* All other aggregate operations return a value <code>NULL</code> when applied to an empty collection.

<br><br>

# Nested Subqueries

SQL provides a mechanism for the nesting of subqueries.

* A <strong>subquery</strong> is a <code>SELECT</code>-<code>FROM</code>-<code>WHERE</code>expression that is nested within another query. 
* The nesting can be done in the following SQL query

    ```sql
	SELECT A_1, A_2, ..., A_n
	FROM r_1, r_2, ..., r_m
	WHERE P
    ```
    
    as follows:
    * <code>FROM</code> clause: <code>r_i</code> can be replaced by any valid subquery
    * <code>WHERE</code>clause: <code>P</code> can be replaced with an expression of the form: <code>B OPERATION (subquery)</code> 
        * <code>B</code>: attribute
        * <code>OPERATION</code>: e.g., <code>IN</code>, <code>NOT IN</code>, etc.
    * <code>SELECT</code> clause: <code>A_i</code> can be replaced be a subquery that generates a single value

## Set Membership

SQL allows testing tuples for membership in a relation, using <code>IN</code> or <code>NOT IN</code> clause.

* Examples
    * e.g., Find the courses offered in Fall 2017 and in Spring 2018

        ```sql
        SELECT DISTINCT course_id
        FROM section
        WHERE semester = 'Fall' AND year = 2017 AND 
                course_id IN (SELECT course_id
                              FROM section
                              WHERE semester = 'Spring' AND year = 2018)
        ```
    
    * e.g., Name all instructors whose name is neither “Mozart” nor Einstein”

        ```sql
        SELECT DISTINCT name
        FROM instructor
        WHERE name NOT IN ('Mozart', 'Einstein') 
        ```

    * e.g., Find the total number of distinct students who have taken course sections taught by the instructor with ID <code>10101</code>
        * As seen in the example query below, it is possible to test for membership in an arbitrary relation.

        ```sql
        SELECT  COUNT(DISTINCT ID)
        FROM    takes
        WHERE   (course_id, sec_id, semester, year) IN
        (
            SELECT  course_id, sec_id, semester, year
            FROM    teaches
            WHERE   teaches.ID = 10101
        ); 
        ```
    
## Set Comparison

### <code>SOME</code> clause

<code>SOME</code> clause can check if the predicate is satisfied by at least one tuple in the relation.

* <code>F comp SOME r</code> $\Leftrightarrow$ $\exists t \in r$ such that $(F \; \textrm{comp} \; t)$, where $\textrm{comp} \in \{<,\leq,>,\geq,=,\neq \}$.
* <code>= SOME</code> $\Leftrightarrow$ <code>IN</code>, however <code><> SOME</code> $\nLeftrightarrow$ <code>NOT IN</code>

* e.g., Find the names of all instructors whose salary is greater than that of some (at least one) instructor in the Biology department

    ```sql
    SELECT  DISTINCT T.name
    FROM    instructor AS T,
                    instructor AS S
    WHERE   T.salary > S.salary AND S.dept_name = 'Biology';
    ```

    ```sql
    -- Using SOME clause
    SELECT  name
    FROM    instructor
    WHERE   salary > SOME (SELECT  salary
                           FROM    instructor
                           WHERE   dept_name = 'Biology');
    ```

### <code>ALL</code> clause

<code>ALL</code> clause can check if the predicate is satisfied by all of the tuples in the relation.
* <code>F comp ALL r</code> $\Leftrightarrow$ $\forall t \in r$ such that $(F \; \textrm{comp} \; t)$, where $\textrm{comp} \in \{<,\leq,>,\geq,=,\neq \}$.
* <code><> ALL</code> $\Leftrightarrow$ <code>NOT IN</code>, however <code>= ALL</code> $\nLeftrightarrow$ <code>IN</code>

* e.g., Find the names of all instructors whose salary is greater the salary of all instructors in the Biology department

    ```sql
    SELECT  name
    FROM    instructor
    WHERE   salary > ALL (SELECT  salary
                          FROM    instructor
                          WHERE   dept_name = 'Biology');
    ```

## Test for Empty Relations

The <code>EXISTS</code> construct returns the value <code>TRUE</code> if the argument subquery is nonempty.
* <code>EXISTS r</code> $\Leftrightarrow$ $r \neq \emptyset$
* <code>NOT EXISTS r</code> $\Leftrightarrow$ $r = \emptyset$

* e.g., Find all students who have taken all courses offered in the Biology department
    * "relation A contains relation B" as <code>NOT EXISTS (B EXCEPT A)</code>

    ```sql
    SELECT  DISTINCT S.ID, S.name
    FROM    student AS S
    WHERE   NOT EXISTS (SELECT  course_id
                        FROM    course
                        WHERE   dept_name = 'Biology'
                        EXCEPT
                        SELECT  T.course_id
                        FROM    takes AS T
                        WHERE   S.ID = T.ID);
    ```

    * **Correlation name**: variable <code>S</code> in the outer query
    * **Correlated subquery**: the inner query
    * Note that $X−Y = \emptyset$ $\Leftrightarrow$ $X \subseteq Y$
    * This query cannot be written using <code>= ALL</code> and its variants

## Test for the Absence of Duplicate Tuples

The <code>UNIQUE</code> construct tests whether a subquery has any duplicate tuples in its result. It evaluates to TRUE if a given subquery contains no duplicates, or an empty set.

* e.g., Find all courses that were offered at most once in 2017

    ```sql
    SELECT  T.course_id
    FROM    course AS T
    WHERE   UNIQUE (SELECT  R.course_id
                    FROM    course AS R
                    WHERE   T.course_id = R.course_id AND
                            R.year = 2017);
    ```

Since the test $t_1 = t_2$ fails if any of the fields of $t_1$ or $t_2$ are null, it is possible for <code>UNIQUE</code > to be true even if there are multiple copies of a tuple, as long as at least one of the attributes of the tuple is null.


## Subqueries in the <code>FROM</code> clause

Since any <code>SELECT</code> - <code>FROM</code> - <code>WHERE</code> clause returns a relation as a result, it can be inserted into another <code>SELECT</code> - <code>FROM</code> - <code>WHERE</code> anywhere that a relation can appear.

* e.g., Find the average instructors’ salaries of those departments where the average salary is greater than <code>42000</code>

    ```sql
    SELECT dept_name, avg_salary
    FROM  (SELECT  dept_name, AVG(salary) AS avg_salary
           FROM    instructor
           GROUP BY dept_name)
    WHERE  avg_salary > 42000;
    ```

    * Since the subquery in the <code>FROM</code> clause computes the average salary, <code>HAVING</code> query is not required; the predicate is rather inside the <code>WHERE</code> clause of the outer query.
    * The above query is equivalent to

        ```sql
        SELECT  dept_name, AVG(salary) AS avg_salary
        FROM    instructor
        GROUP BY dept_name
        HAVING  avg_salary > 42000
        ```

## <code>WITH</code> clause

The <code>WITH</code> clause provides a way of defining a temporary relation whose definition is available only to the query in which the <code>WITH</code> clause occurs. 

* e.g., Find all departments with the maximum budget

    ```sql
    WITH    max_budget(value) AS ( SELECT  MAX(budget)
                                   FROM    department)
    SELECT  department.dept_name
    FROM    department, max_budget
    WHERE   department.budget = max_budget.value
    ```

* e.g., Find all departments where the total salary is greater than the average of the total salary at all departments

    * You don't need to repeat the <code>WITH</code> keyword (it might raise a syntax error):

    ```sql
    WITH    dept_total(dept_name, value) AS
    (
        SELECT  dept_name,
                SUM(salary)
        FROM    departments
        GROUP BY dept_name
    ),
            dept_total_avg(value) AS
    (
        SELECT AVG(value)
        FROM   dept_total
    )
    SELECT  dept_name
    FROM    dept_total,
            dept_total_avg
    WHERE   dept_total.value > dept_total_avg.value;
    ```

## Scalar subquery

<strong>Scalar subqeury</strong> is used where a single value is expected. It incurs a runtime error if the subquery returns more than one result tuple.

* e.g., Find all departments, along with the number of instructors, in each department

    ```sql
    SELECT  dept_name,
            (SELECT  COUNT(*)
             FROM    instructor
             WHERE   department.dept_name = instructor.dept_name
             GROUP BY dept_name
            ) AS num_instructors
    FROM    department;
    ```

<br><br>

# Modification of the database

## Deletion

A <strong>delete</strong> request can only delete whole tuples; deleting values on only particular attributes is not allowed.

```sql
DELETE FROM r
WHERE       p
```

* <code>r</code>: Relation
* <code>p</code>: Predicate

The <code>DELETE</code> statement first finds all tuples <code>t</code> in <code>r</code> for which <code>P(t)</code> is true, and then deletes them from <code>r</code>. Note that <code>DELETE</code> command operates on only one relation.

* <strong>Examples</strong>
    * e.g., Delete all tuples from <code>instructor</code> table

        ```sql
        DELETE FROM instructor;
        ```

    * e.g., Delete all tuples in the instructor relation for those instructors associated with a department located in the Watson building

        ```sql
        DELETE FROM instructor
        WHERE       dept_name IN (SELECT  dept_name
                                  FROM    department
                                  WHERE   building = 'Watson');
        ```

    * e.g., Delete all instructors whose salary is less than the average salary of all instructors

        ```sql
        DELETE FROM instructor
        WHERE       salary <
        (
            SELECT  AVG(salary)
            FROM    instructor
        );
        ```

    SQL first computes the average salary and find all tuples to delete, then delete all corresponding tuples from the relation (without recomputing or retesting the tuples).

## Insertion

To insert data into a relation, either a tuple to be inserted, or the query whose result is a set of tuples to be inserted, must be specified. The <code>SELECT</code> - <code>FROM</code> - <code>WHERE</code> statement is evaluated fully before any of its results are inserted into the relation.

```sql
INSERT INTO r
```

* <code>r</code>: Relation

* <strong>Examples</strong>
    * e.g., Insert a new tuple to course

        ```sql
        INSERT INTO course
            VALUES('CS360', 'Introduction to Database', 'SoC', 3)
        ```

    * e.g., Make each student in the Music department who has earned more than 144 credit as an instructor in the Music department with a salary of 18000
        
        ```sql
        INSERT INTO instructor
            SELECT  ID,
                    name,
                    dept_name,
                    18000
            FROM    student
            WHERE   dept_name = 'MUSIC'
                    AND tot_credit > 144;
        ```

## Update

A value in a tuple can be changed without changing all values in the tuple with the <code>UPDATE</code> statement.

```sql
UPDATE  r
SET     A = (Some Value)
WHERE   p
```

* <code>r</code>: Relation
* <code>A</code>: Attribute
* <code>p</code>: Predicate

Here are some examples;

* <strong>Examples</strong>
    * e.g., Give a 5% salary raise to those instructors who earn less than 70000

        ```sql
        UPDATE  instructor
        SET     salary = salary * 1.05
        WHERE   salary < 70000;
        ```

    * e.g. Give a 5% salary raise to instructors whose salary is less than the average of all instructors

        ```sql
        UPDATE  instructor
        SET     salary = salary * 1.05
        WHERE   salary <
        (
            SELECT  AVG(salary)
            FROM    instructor
        );
        ```

Note that the order of <code>UPDATE</code> statements is very important. Consider the following query.

```sql
-- Update 1
UPDATE  instructor
SET     salary = salary * 1.03
WHERE   salary > 100000
-- Update 2
UPDATE  instructor
SET     salary = salary * 1.05
WHERE   salary <= 100000
```

If the order of the two updates are changed, the results whould not be as desired. To prevent order related problems, <code>CASE</code> construct is provided by SQL.

### CASE construct

<code>CASE</code> construct can be used in any place where a value is expected.

```sql
CASE
    WHEN  P_1 THEN  R_1
    ...
    WHEN  P_n THEN  R_n
    ELSE            R_0
END
```

* <code>P_i</code>: Predicates
* <code>R_i</code>: Resulting value

The error-prone query above can be re-written using <code>CASE</code> construct.

```sql
UPDATE  instructor
SET     salary =
(
    CASE
        WHEN salary <= 100000 THEN salary * 1.05
        ELSE salary * 1.03
    END
);
```

### Updates with scalar subqueries

Scalar subqueries are also useful in SQL update statements, where they can be used in <code>SET</code> clause.

* <strong>Example</strong>
    * e.g., Recompute and update <code>tot_credit</code> for all students to the credits of courses successfully completed by the student (successfully completed means <code>grade</code> is not <code>F</code> nor <code>NULL</code>)

        ```sql
        UPDATE  student S
        SET     tot_credit = (
            SELECT  SUM(credits)
            FROM    takes JOIN course USING(course_id)
            WHERE   S.ID = takes.ID AND
                    (
                        takes.grade <> 'F' AND
                        takes.grade IS NOT NULL
                    )
        );
        ```

<br><br><br>

# Reference
**[1]** [Silberschatz, Abraham, Henry F. Korth, and Shashank Sudarshan. "Database system concepts." (2011).](https://db-book.com/) <br>
