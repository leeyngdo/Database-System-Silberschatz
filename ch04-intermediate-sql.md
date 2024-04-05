# Table of Contents
<details><summary>click to expand</summary>

* [4.1](#41) <br>
* [4.2](#42) <br>
* [4.3](#43) <br>
* [4.4](#44) <br>
* [4.5](#45) <br>
* [4.6](#46) <br>
* [4.7](#47) <br>
* [4.8](#48) <br>
* [4.9](#49) <br>
* [4.10](#410) <br>
* [4.11](#411) <br>
* [4.12](#412) <br>
* [4.13](#413) <br>
* [4.14](#414) <br>
* [4.15](#415) <br>
* [4.16](#416) <br>
* [4.17](#417) <br>
* [4.18](#418) <br>
* [4.19](#419) <br>
* [4.20](#420) <br>
* [4.21](#421) <br>
* [4.22](#422) <br>
* [4.23](#423) <br>
* [4.24](#424) <br>
* [4.25](#425) <br>

</details><br>

# 4.1

**Question**. Consider the following SQL query that seeks to find a list of titles of all courses taught in Spring 2017 along with the name of the instructor.

```sql
SELECT name, title
FROM instructor NATURAL JOIN teaches NATURAL JOIN section NATURAL JOIN course
WHERE semester = 'Spring' AND year = 2017
```

What is wrong with this query?

<details><summary><strong>Answer</strong>. click to expand</summary>

> Since two relations <code>instructor NATURAL JOIN teaches NATURAL JOIN section</code> and <code>course</code> have the common attributes of name not only <code>id</code> but also <code>dept_name</code>, the course of which department that opened the course and department of the course instructor are distinct would not be found in the list. Thus results are shown only when an instructor teaches a course in her or his own department.

</details><br>

# 4.2

**Question**. Write the following queries in SQL:

<ol style="list-style-type: lower-alpha;">
    <li>Display a list of all instructors, showing each instructor's ID and the number of sections taught. Make sure to show the number of sections as 0 for instructors who have not taught any section. Your query should use an outer join, and should not use subqueries.</li>
    <li>Write the same query as in part a, but using a scalar subquery and not using outer join.</li>
    <li>Display the list of all course sections offered in Spring 2018, along with the ID and name of each instructor teaching the section. If a section has more than one instructor, that section should appear as many times in the result as it has instructors. If a section does not have any instructor, it should still appear in the result with the instructor name set to "-".</li>
    <li>Display the list of all departments, with the total number of instructors in each department, without using subqueries. Make sure to show departments that have no instructors, and list those departments with an instructor count of zero.</li>
</ol>

<details><summary><strong>Answer</strong>. click to expand</summary>

> **a**

```sql
SELECT instructor.id, COUNT (sec_id) AS num_secs
FROM instructor
LEFT OUTER JOIN teaches
ON instructor.id = teaches.id
GROUP BY instructor.id
```

> **b**

```sql
SELECT id, (
    SELECT COUNT (*)
    FROM teaches
    WHERE teaches.id = instructor.id
) AS num_secs
FROM instructor
```

Result:

| **course_id** | **num_secs** |
|:-------------:|:------------:|
|  10101	    |  3	       |
|  12121	    |  1	       |
|  15151	    |  1	       |
|  22222	    |  1	       |
|  32343	    |  1	       |
|  33456	    |  0	       |
|  45565	    |  2	       |
|  58583	    |  0	       |
|  76543	    |  0	       |
|  76766	    |  2	       |
|  83821	    |  3	       |
|  98345	    |  1	       |

> **c.** 

Display the list of all course sections offered in Spring 2018, along with the ID and name of each instructor teaching the section. If a section has more than one instructor, that section should appear as many times in the result as it has instructors. If a section does not have any instructor, it should still appear in the result with the instructor name set to "-".

```sql
SELECT 
    course_id, 
    instructor.id, 
    CASE 
        WHEN instructor.name is NULL THEN '-'
        ELSE name
    END AS name
FROM section
NATURAL LEFT OUTER JOIN teaches
NATURAL JOIN instructor
WHERE semester = 'Spring' AND year = 2018
```

Result:

| **course_id** | **ID** |  **name**  |
|:-------------:|:------:|:----------:|
| CS-101        | 45565  | Katz       |
| CS-315        | 10101  | Srinivasan |
| CS-319        | 45565  | Katz       |
| CS-319        | 83821  | Brandt     |
| FIN-201       | 12121  | Wu         |
| HIS-351       | 32343  | El Said    |
| MU-199        | 15151  | Mozart     |

> **d.**

Display the list of all departments, with the total number of instructors in each department, without using subqueries. Make sure to show departments that have no instructors, and list those departments with an instructor count of zero.

```sql
SELECT dept_name, COUNT (DISTINCT id) AS num_instructors
FROM department
NATURAL LEFT OUTER JOIN instructor
GROUP BY dept_name
```

Result:

| **dept_name** | **num_instructors** |
|:-------------:|:-------------------:|
| Biology       | 1                   |
| Comp. Sci.    | 3                   |
| Elec. Eng.    | 1                   |
| Finance       | 2                   |
| History       | 2                   |
| Music         | 1                   |
| Physics       | 2                   |


</details><br>

# 4.3

**Question**. Outer join expressions can be computed in SQL without using the SQL <code>OUTER JOIN</code> operation. To illustrate this fact, show how to rewrite each of the following SQL queries without using the <code>OUTER JOIN</code> expression.


<ol style="list-style-type: lower-alpha;">
    <li><code>SELECT * FROM student NATURAL LEFT OUTER JOIN takes</code></li>
    <li><code>SELECT * FROM student NATURAL FULL OUTER JOIN takes</code></li>
</ol>

<details><summary><strong>Answer</strong>. click to expand</summary>

> **a.** 

```sql
SELECT * FROM student NATURAL JOIN takes
UNION
SELECT student.*, NULL, NULL, NULL, NULL, NULL
FROM student
WHERE student.id NOT IN (SELECT id FROM takes)
```

> **b.** 

```sql
SELECT * FROM student NATURAL JOIN takes
UNION
SELECT student.*, NULL, NULL, NULL, NULL, NULL
FROM student
WHERE student.id NOT IN (SELECT id FROM takes)
UNION 
SELECT id, NULL, NULL, NULL, course_id, sec_id, semester, year, grade
FROM takes
WHERE takes.id NOT IN (SELECT id FROM student)
```

</details><br>

# 4.4

**Question**. Suppose we have three relations $`r(A, B)`$, $`s(B, C)`$, and $`t(B, D)`$, with all attributes declared as <code>NOT NULL</code>.


<ol style="list-style-type: lower-alpha;">
<li> Give instances of relations $`r`$, $`s`$, and $`t`$ such that in the result of

```sql 
(r NATURAL LEFT OUTER JOIN s) NATURAL LEFT OUTER JOIN t 
```

attribute $`C`$ has a null value but attribute D has a non-null value.</li>
<li>Are there instances of $`r`$, $`s`$, and $`t`$ such that the result of

```sql
r NATURAL LEFT OUTER JOIN (s NATURAL LEFT OUTER JOIN t)
```

has a null value for $`C`$ but a non-null value for $`D`$? Explain why or why not.
</li>
</ol>

<details><summary><strong>Answer</strong>. click to expand</summary>

> **a.** $`r = (1, 2)`$, $`s = (3, 1)`$, $`t = (2, 1)`$. Then natural left join $`r`$ and $`s`$ results in $`(1, 2, \text{ NULL})`$, and the final result outputs $`(1, 2, \text{ NULL }, 1)`$.

> **b.** No. Natural left join $`r`$ and natural left join $`s$ between $`t`$ will assign the <code>NULL</code> value to the attributes absent in $`r`$ simulatenously, thus $`C`$ and $`D`$ should be either <code>NULL</code> or <code>NOT NULL</code>.

</details><br>

# 4.5

**Question**. <strong>Testing SQL queries</strong>: To test if a query specified in English has been correctly written in SQL, the SQL query is typically executed on multiple test databases, and a human checks if the SQL query result on each test database matches the intention of the specification in English.

<ol style="list-style-type: lower-alpha;">
    <li>In Section 4.1.1 we saw an example of an erroneous SQL query which was intended to find which courses had been taught by each instructor; the query computed the natural join of <code>instructor</code>, <code>teaches</code>, and <code>course</code>,and as a result it unintentionally equated the <code>dept_name</code> attribute of <code>instructor</code> and <code>course</code>. Give an example of a dataset that would help catch this particular error.</li>
    <li>When creating test databases, it is important to create tuples in referenced relations that do not have any matching tuple in the referencing relation for each foreign key. Explain why, using an example query on the university database.</li>
    <li>When creating test databases, it is important to create tuples with null values for foreign-key attributes, provided the attribute is nullable (SQL allows foreign-key attributes to take on null values, as long as they are not part of the primary key and have not been declared as <code>NOT NULL</code>). Explain why, using an example query on the university database.</li>
</ol>

<details><summary><strong>Answer</strong>. click to expand</summary>

> **a.** When the instructor of Physics teaches the course in Math department, this tuple will not be present in the result of erroneous query. 

> **b.** Because, it is useful in some scenarios, such as Exercise 4.2 (a). If the creation of tuples in referenced relations that do not have any matching tuple in the referencing relation for each foreign key, it is impossible to maintain the instructors in the database who do not teach any courses.

```sql
SELECT instructor.id, COUNT (sec_id) AS num_secs
FROM instructor
LEFT OUTER JOIN teaches
ON instructor.id = teaches.id
GROUP BY instructor.id
```

> **c.** Because, it is useful in some scenarios, such as the following query. Suppose there is a student 'Alice' who doesn't decide her department yet. Now, let's say we want to retrieve the names of all students along with the names of their department. Then, without allowing <code>NULL</code> values for non-primary foreign-key attributes, t would not be possible to represent some students like Alice.

```sql
INSERT INTO students (student_id, name, dept_name) VALUES 
(102, 'Alice', NULL);

SELECT s.name AS student_name, d.dept_name
FROM students s
LEFT JOIN department d ON s.dept_name = c.dept_name;
```

</details><br>

# 4.6

**Question**. Show how to define the view <code>student_grades (ID, GPA)</code> giving the grade-point average of each student, based on the query in Exercise 3.2; recall that we used arelation <code>grade_points(<u>grade</u>, points)</code> to get the numeric points associated with a letter grade. Make sure your view definition correctly handles the case of <code>NULL</code> values for the <code>grade</code> attribute of the <code>takes</code> relation.

<details><summary><strong>Answer</strong>. click to expand</summary>

```sql
create view student_grades (ID, GPA) as (
    SELECT ID, 
           credit_points / decode(credit_sum, 0, NULL, credit_sum) 
    FROM (SELECT ID, 
            sum(decode(grade, NULL, 0, credits)) AS credit_sum, 
            sum(decode(grade, NULL, 0, credits * points) AS credit_points 
          FROM (takes NATURAL JOIN course) NATURAL LEFT OUTER JOIN grade_points 
          GROUP BY ID))
    UNION
    SELECT ID, NULL, NULL 
    FROM student
    WHERE ID NOT IN
        (SELECT ID FROM takes))
```

</details><br>

# 4.7

**Question**. Consider the employee database of Figure 4.12. Give an SQL DDL definition of this database. Identify referential-integrity constraints that should hold, and include them in the DDL definition.

<p align = "center">
    <img width="800" alt="Figure 4.12." src="assets/figure-4-12.png">
</p><br>

<details><summary><strong>Answer</strong>. click to expand</summary>

```sql
CREATE TABLE employee
(
    ID              INTEGER,
    person_name     VARCHAR(20),
    street          VARCHAR(20),
    city            VARCHAR(20),
    PRIMARY KEY (ID)
)

CREATE TABLE works
(
    ID              INTEGER,
    company_name    VARCHAR(20),
    salary          NUMERIC(7, 2),
    PRIMARY KEY (ID)
    FOREIGN KEY (ID) REFERENCES employee (id)
    FOREIGN KEY (company_name) REFERENCES company (company_name)
)

CREATE TABLE company
(
    company_name     VARCHAR(20),
    city            VARCHAR(20),
    PRIMARY KEY (company_name)
)

CREATE TABLE manages
(
    ID              INTEGER,
    manager_id      INTEGER,
    PRIMARY KEY (ID),
    FOREIGN KEY (ID) REFERENCES employee (id),
    FOREIGN KEY (manager_id) REFERENCES employee (id)
)
```

</details><br>

# 4.8

**Question**. As discussed in Section 4.4.8, we expect the constraint "an instructor cannot teach sections in two different classrooms in a semester in the same time slot" to hold.

<ol style="list-style-type: lower-alpha;">
    <li>Write an SQL query that returns all (instructor, section) combinations that violate this constraint.</li>
    <li>Write an SQL assertion to enforce this constraint (as discussed in Section 4.4.8, current generation database systems do not support such as-sertions, although they are part of the SQL standard).</li>
</ol>

<details><summary><strong>Answer</strong>. click to expand</summary>

> **a.**

```sql
SELECT teaches.*, section.*, COUNT (DISTINCT building, room_number)
FROM section
NATURAL JOIN teaches
GROUP BY sec_id, semester, year, ID, time_slot_id
HAVING COUNT (building, room_number) > 1
```

If multiple arguments in <code>COUNT</code> is not allowed, instead:

```sql
SELECT teaches.*, section.*, COUNT (DISTINCT building || ' ' || room_number)
FROM section
NATURAL JOIN teaches
GROUP BY sec_id, semester, year, ID, time_slot_id
HAVING COUNT (building || ' ' || room_number) > 1
```

> **b.** 

```sql
CREATE ASSERTION instructor_constraint CHECK
(
    NOT EXISTS (
        SELECT teaches.*, section.*, COUNT (DISTINCT building, room_number)
        FROM section
        NATURAL JOIN teaches
        GROUP BY sec_id, semester, year, ID, time_slot_id
        HAVING COUNT (building, room_number) > 1
    )
)
```

</details><br>

# 4.9

**Question**. SQL allows a foreign-key dependency to refer to the same relation, as in the following example:

```sql
CREATE TABLE manager
    (employee_ID        CHAR (20),
     manager_ID         CHAR (20),
     PRIMARY KEY employee_ID,
     FOREIGN KEY (manager_ID) REFERENCES manager (employee_ID)
        ON DELETE CASCADE
    )
```

Here, <code>employee_ID</code> is a key to the table <code>manager</code>, meaning that each employee has at most one manager. The foreign-key clause requires that every manager also be an employee. Explain exactly what happens when a tuple in the relation <code>manager</code> is deleted.

<details><summary><strong>Answer</strong>. click to expand</summary>

> Upon deletion of a tuple within the <code>manager</code> relation, every employee whose manager is removed will also be deleted by virtue of the cascade clause. However, this simple deletion can be extended to the entire relation: Given that the <code>manager_ID</code> of any tuple must correspond to an <code>employee_ID</code> within the relation, the tuples in the <code>manager</code> will estabilish either a cycle or one-way infinite chain. Consequently, the deletion of a single tuple results in the deletion of all tuples within the relation.

</details><br>

# 4.10

**Question**.  Given the relations <code>a(name, address, title)</code> and <code>b(name, address, salary)</code>, show how to express <code>a NATURAL FULL OUTER JOIN b</code> using the <code>FULL OUTER JOIN</code> operation with an <code>ON</code> condition rather than using the <code>NATURAL JOIN</code> syntax. This can be done using the coalesce operation. Make sure that the result relation does not contain two copies of the attributes name and address and that the solution is correct even if some tuples in a and b have null values for attributes name or address.

<details><summary><strong>Answer</strong>. click to expand</summary>

```sql
SELECT COALESCE(a.name, b.name) AS name, COALESCE(a.address, b.address) AS address, title, salary
FROM a 
FULL OUTER JOIN b
ON a.name = b.name AND a.address = b.address
```

</details><br>

# 4.11

**Question**. Operating systems usually offer only two types of authorization control for data files: read access and write access. Why do database systems offer so many kinds of authorization?

<details><summary><strong>Answer</strong>. click to expand</summary>

The various kinds of authorization in database systems originate from the nature of database.

> **Data Sharing**: Databases may need to support data sharing among different users or applications while ensuring data isolation and confidentiality. Granular authorization controls facilitate secure data sharing by defining access permissions at a fine-grained level.
> **Data Integrity**: Databases often store critical and sensitive information. Granular authorization controls help ensure that only authorized users can access, modify, or delete specific data, maintaining data integrity and consistency.
> **Business Logic**: Databases support complex business processes that require different levels of access to data based on roles and responsibilities within an organization. Granular authorization allows administrators to define access levels tailored to specific user roles or groups.

</details><br>

# 4.12

**Question**. Suppose a user wants to grant <code>SELECT</code> access on a relation to another user. Why should the user include (or not include) the clause <code>GRANTED BY CURRENT_ROLE</code> in the <code>GRANT</code> statement?

<details><summary><strong>Answer</strong>. click to expand</summary>

> Suppose the granting of the role instructor (or other privileges) to Amit is done using the granted by current role clause, with the current role set to dean,instead of the grantor being the user Satoshi. Then, revoking of roles/privileges (including the role dean) from Satoshi will not result in revoking of privileges that had the grantor set to the role dean, even if Satoshi was the user who executed the grant; thus, Amit would retain the instructor role even after Satoshi’s privileges are revoked.

</details><br>

# 4.13

**Question**. Consider a view <code>v</code> whose definition references only relation <code>r</code>.

* If a user is granted <code>SELECT</code> authorization on <code>v</code>, does that user need to have <code>SELECT</code> authorization on <code>r</code> as well? Why or why not? 
* If a user is granted <code>UPDATE</code> authorization on <code>v</code>, does that user need to have <code>UPDATE</code> authorization on <code>r</code> as well? Why or why not?
* Give an example of an <code>INSERT</code> operation on a view <code>v</code> to add a tuple <code>t</code> that is not visible in the result of <code>SELECT * FROM v</code>. Explain your answer.


<details><summary><strong>Answer</strong>. click to expand</summary>

> No. <code>SELECT</code> authorization on <code>v</code> allows a user to be granted access to only part of relation <code>r</code>. For example, the instructors information (e.g. salary) of 'Music' department should not be visible to the clerk works for 'Physics' department.  

> Yes. Since the update content in <code>v</code> should be applied to <code>r</code>, the grantee must have <code>UPDATE</code> authorization on <code>r</code>. If not, the update on <code>v</code> by the grantee would be rejected.

> Any tuple <code>t</code> compatible with the schema for <code>v</code> but not satisfying the <code>WHERE</code> clause in the definition of view <code>v</code> is a valid example:

```sql
CREATE VIEW history_instructors AS SELECT * FROM instructor WHERE dept_name = 'History';
INSERT INTO history_instructors VALUES (1234, 'Taylor', 'Physics');
```

</details><br>

# 4.14

**Question**. Consider the query

```sql
SELECT course_id, semester, year, sec_id, avg (tot_cred) 
FROM takes 
NATURAL JOIN student
WHERE year = 2017
GROUP BY course_id, semester, year, sec_id 
HAVING count (ID) >= 2;
```

Explain why appending <code>NATURAL JOIN section</code> in the <code>FROM</code> clause would not change the result.

<details><summary><strong>Answer</strong>. click to expand</summary>

> Since attributes <code>course_id, semester, year, sec_id</code> of <code>section</code> foreign keys referencing <code>section</code> (hence all <code>takes</code> tuples have correspoding tuples in <code>section</code> relation) and there is no use of <code>section</code> information rather than these attributes, <code>NATURAL JOIN</code> operation, which matches tuples with the same values for all common attributes, would not change the result. 


</details><br>

# 4.15

**Question**. Rewrite the query

```sql
SELECT * 
FROM section NATURAL JOIN classroom
```

without using a natural join but instead using an inner join with a <code>using</code> condition.

<details><summary><strong>Answer</strong>. click to expand</summary>

```sql
SELECT *
FROM section INNER JOIN classroom USING (building, room_number)
```

Result:

| **course_id** | **sec_id** | **semester** | **year** | **building** | **room_number** | **time_slot_id** | **capacity** |
|:-------------:|:----------:|:------------:|:--------:|:------------:|:---------------:|:----------------:|:------------:|
| BIO-101       | 1          | Summer       | 2017     | Painter      | 514             | B                | 10           |
| BIO-301       | 1          | Summer       | 2018     | Painter      | 514             | A                | 10           |
| CS-101        | 1          | Fall         | 2017     | Packard      | 101             | H                | 500          |
| CS-101        | 1          | Spring       | 2018     | Packard      | 101             | F                | 500          |
| CS-190        | 1          | Spring       | 2017     | Taylor       | 3128            | E                | 70           |
| CS-190        | 2          | Spring       | 2017     | Taylor       | 3128            | A                | 70           |
| CS-315        | 1          | Spring       | 2018     | Watson       | 120             | D                | 50           |
| CS-319        | 1          | Spring       | 2018     | Watson       | 100             | B                | 30           |
| CS-319        | 2          | Spring       | 2018     | Taylor       | 3128            | C                | 70           |
| CS-347        | 1          | Fall         | 2017     | Taylor       | 3128            | A                | 70           |
| EE-181        | 1          | Spring       | 2017     | Taylor       | 3128            | C                | 70           |
| FIN-201       | 1          | Spring       | 2018     | Packard      | 101             | B                | 500          |
| HIS-351       | 1          | Spring       | 2018     | Painter      | 514             | C                | 10           |
| MU-199        | 1          | Spring       | 2018     | Packard      | 101             | D                | 500          |
| PHY-101       | 1          | Fall         | 2017     | Watson       | 100             | A                | 30           |

</details><br>

# 4.16

**Question**. Write an SQL query using the university schema to find the ID of each student who has never taken a course at the university. Do this using no subqueries and no set operations (use an outer join).

<details><summary><strong>Answer</strong>. click to expand</summary>

```sql
SELECT id
FROM student
NATURAL LEFT OUTER JOIN takes
WHERE sec_id is NULL
```

Result:

| **ID** |
|:------:|
| 70557  |

</details><br>

# 4.17

**Question**. Express the following query in SQL using no subqueries and no set operations.

```sql
SELECT id 
FROM student
EXCEPT 
SELECT s_id
FROM advisor
where i_id IS NOT NULL
```

<details><summary><strong>Answer</strong>. click to expand</summary>

```sql
SELECT id
FROM student
LEFT OUTER JOIN advisor ON student.id = advisor.s_id
WHERE i_id IS NULL
```

</details><br>

# 4.18

**Question**. For the database of Figure 4.12, write a query to find the ID of each employee with no manager. Note that an employee may simply have no manager listed or may have a <code>NULL</code> manager. Write your query using an outer join and then write it again using no outer join at all.

<details><summary><strong>Answer</strong>. click to expand</summary>

```sql
SELECT employee.ID
FROM employee
NATURAL LEFT OUTER JOIN manages
WHERE manager_id IS NULL
```

```sql
SELECT id
FROM employee
WHERE id NOT IN (
    SELECT id
    FROM manages
    WHERE manager_id IS NOT NULL
);
```

</details><br>

# 4.19

**Question**. Under what circumstances would the query

```sql
SELECT * 
FROM student NATURAL FULL OUTER JOIN takes
NATURAL FULL OUTER JOIN course
```

include tuples with <code>NULL</code> values for the <code>title</code> attribute?

<details><summary><strong>Answer</strong>. click to expand</summary>

> First, for students devoid of any enrolled courses, natural full outer join between <code>student</code> and <code>takes</code> yields tuples of student along with <code>NULL</code> course information of <code>takes</code>, which leads to <code>NULL</code> title for natural full outer join with <code>course</code>. Second, if the course already possesses <code>NULL</code> for the title attribute. This is possible if there is devoid of <code>NOT NULL</code> constraint in the schema of <code>course</code>. Third, if a student takes courses not offered by their respective department. Note that <code>dept_name</code> attribute appears in both course relation and student relation, thus natural join also matches the <code>dept_name</code> attribute.

</details><br>


# 4.20

**Question**. Show how to define a view <code>tot_credits (year, num_credits)</code>, giving the total number of credits taken in each year.

<details><summary><strong>Answer</strong>. click to expand</summary>

```sql
CREATE VIEW tot_credits(year, num_credits) AS (
    SELECT year, SUM (credits)
    FROM takes
    NATURAL JOIN courses
    GROUP BY year
    ORDER BY year ASC
);
```

</details><br>


# 4.21

**Question**. For the view of Exercise 4.18, explain why the database system would not allow a tuple to be inserted into the database through this view.

<details><summary><strong>Answer</strong>. click to expand</summary>

Views present significant problems if updates are expressed with them. The difficulty is that a modification to the database expressed in terms of a view must be translated to a modification to the actual relations in the logical model of the database.

1. Since the view may not have all the attributes of the underlying tables, insertion of a tuple into the view will insert tuples into the underlying tables, with those attributes not participating in the view getting null values. This may not be desirable, especially if the attribute in question is part of the primary key of the table. 
2. If a view is a join of several underlying tables and an insertion results in tuples with nulls in the join columns, the desired effect of the insertion will not be achieved. In other words, an update to a view may not be expressible at all as updates to base relations. 

</details><br>

# 4.22

**Question**. Show how to express the <code>COALESCE</code> function using the <code>CASE</code> construct.

<details><summary><strong>Answer</strong>. click to expand</summary>

Note that <code>COALESCE</code> function returns the first of its arguments that is not <code>NULL</code>. If all arguments are <code>NULL</code>. return <code>NULL</code>. Therefore,

```sql
COALESCE (e_1, e_2, ..., e_n)
```

is equivalent to

```sql
CASE 
    WHEN e_1 IS NOT NULL THEN e_1
    WHEN e_2 IS NOT NULL THEN e_2
    ...
    WHEN e_n IS NOT NULL THEN e_n
    ELSE NULL
END
```

</details><br>

# 4.23

**Question**. Explain why, when a manager, say Satoshi, grants an authorization, the grant should be done by the manager role, rather than by the user Satoshi.

<details><summary><strong>Answer</strong>. click to expand</summary>

> If authorization is granted by the user Satoshi instead of the manager role, and Satoshi leaves the company, revoking Satoshi's authorization will result in revoking the authorization for all employees granted by Satoshi. To avoid this scenario, if the administrator wishes to prevent the revocation of authorization for all employees granted by Satoshi, the manager role should grant authorization instead of the user Satoshi.

</details><br>


# 4.24

**Question**. Suppose user $`A`$, who has all authorization privileges on a relation $`r`$, grants <code>SELECT</code> on relation $`r`$ to <code>PUBLIC</code> with grant option. Suppose user $`B`$ then grants <code>SELECT</code> on $`r`$ to $`A`$. Does this cause a cycle in the authorization graph? Explain why.

<details><summary><strong>Answer</strong>. click to expand</summary>

> No, because the result graph forms one-way cycle. Revoking $`B`$'s all privileges does not revoke $`A`$'s privileges as he/she already has its own privileges; thus there exist some paths from user $`A`$ to DBA independent of $`B`$.

</details><br>

# 4.25

**Question**. Suppose a user creates a new relation $`r1`$ with a foreign key referencing another relation $`r2`$. What authorization privilege does the user need on $`r2`$? Why should this not simply be allowed without any such authorization?

<details><summary><strong>Answer</strong>. click to expand</summary>

> The <code>REFERENCES</code> privilege is necessitated. It is required  because foreign key constraints restrict deletion and update operations on the referenced relation, which may restrict future activity by other users. Furthermore, to check for the existence of a certain value in a certain (set of) attributes of the referenced relation, user should require some permitances to read the referencing relation. 


</details><br>

# 4.26

**Question**. Explain the difference between integrity constraints and authorization constraints.

<details><summary><strong>Answer</strong>. click to expand</summary>

> Integrity constraints are constraints for relations, while authorization constraints are for users. And the goal of integrity constraints is to ensure the accuracy, consistency, and reliability of data stored in the database, while authorization constraints control access to database objects and operations based on user privileges and roles, ensuring that users only access or modify data they are authorized to.

</details><br>
