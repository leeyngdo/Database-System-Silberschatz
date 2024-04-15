---
title:  "[DB] Advanced SQL" 
date: 2024-03-27
last_modified_at: 2024-03-27
---

# Accessing SQL From a Programming Language

A database programmer must have access to a general purpose programming language for at least two reasons:

* Not all queries can be expressed in SQL. <br>
  SQL does not provide the full expressive power of a general-purpose language.
* Non-declarative actions cannot be done from within SQL. <br>
  Non-delcatarive actions include printing a report, interacting with a user, and sending the results of a query to a GUI.

There are two approaches to accessing SQL from a general purpose programming language:

* <strong>Dynamic SQL</strong>: SQL statements that are constructed at runtime; for example, the application may allow users to enter their own queries <br>
  * A general purpose program can connect to and communicate with a database server using a collection of functions. 
    * Enabling you to build SQL statements dynamically at runtime
    * Being able to create more general purpose, flexible applications by using dynamic SQL because the full text of a SQL statement may be unknown at compilation
    * e.g. Dynamic SQL statement in Python
* <strong>Embedded SQL</strong>: SQL statements in an application that do not change at runtime and, therefore, can be hard-coded into the application<br>
  * Like dynamic SQL, it provides a means by which program can interact with a database server. However, under embedded SQL:
    * The SQL statements are identified at compile time, and are translated into function calls.
    * At runtime, these function calls connect to the database using an API that provides dynamic SQL facilities (but may be specific to the database being used).
    * e.g. Oracle SQL

<br>

# Triggers

* A <strong>trigger</strong> is a statement that is executed automatically by the system as a side effect of a modification to the database.
  * e.g. whenever a tuple is inserted into the <code>takes</code> relation, updates the <code>tot_creds</code> attribute of tuple in the <code>student</code> relation.
* The two requirements of a trigger design are:
  * Specify the <strong>conditions</strong> under which the trigger is to be executed.
  * Specify the <strong>actions</strong> to be taken when the trigger executes.

## Triggering Events and Actions in SQL

* Triggering event can be an insert, a deletion, or an update.
* Triggers on updates can be restricted to specific attributes.
  * e.g. <code>AFTER UPDATE OF takes ON grade</code>
* Values of attributes before and after an update can be referenced.
    * <code>REFERENCING OLD ROW AS</code>: For deletions and updates
    * <code>REFERENCING NEW ROW AS</code>: For insertions and updates
* <code>BEGIN ATOMIC ... END</code> can serve to collect multiple SQL statements into a single compound statement.
* Triggers can be activated before and after an event.
    * <code>AFTER UPDATE OF</code>
    * <code>BEFORE UPDATE OF</code>: Serves as an extra constraint that can prevent invalid updates, inserts, or deletes
      * e.g., suppose the value of an inserted grade is blank, presumably to indicate the absence of a grade. We can define a trigger that replaces the value with <code>NULL</code>

        ```sql
        CREATE TRIGGER setnull_trigger BEFORE UPDATE OF takes
          REFERENCING NEW ROW AS nrow
          FOR EACH ROW
            WHEN (nrow.grade = ' ')
            BEGIN ATOMIC
                SET nrow.grade = NULL;
            END;
        ```

<strong>Example</strong><br>

* When the <code>grade</code> attribute is updated for a tuple in the <code>takes</code> relation, keep the <code>tot_cred</code> attribute value of <code>student</code> tuples up-to-date:

```sql
-- When (Trigger Conditions)
CREATE TRIGGER credits_earned AFTER UPDATE OF takes ON (grade)
  -- What (Trigger Actions)
  REFERENCING NEW ROW AS nrow
  REFERENCING OLD ROW AS orow
  FOR EACH ROW
    WHEN    (nrow.grade <> 'F' AND nrow.grade IS NOT NULL)
    AND     (orow.grade = 'F'  OR  orow.grade IS NULL)
    BEGIN ATOMIC
        UPDATE  student
        SET     tot_cred = tot_cred +
        (
            SELECT  credits
            FROM    course
            WHERE   course.course_id = nrow.course_id
        )
        WHERE   student.id = nrow.id;
    END;
```

* Using triggers to maintain referential integrity

```sql
CREATE TRIGGER timeslot_check1 AFTER INSERT ON section 
  REFERENCING NEW ROW AS nrow 
  FOR EACH ROW
    WHEN (nrow.time slot_id NOT IN (
          SELECT time_slot_id 
          FROM time_slot)) /* time_slot_id not present in time_slot */
    BEGIN
      rollback 
    END;
  
CREATE TRIGGER timeslot_check2 AFTER DELETE ON timeslot 
  REFERENCING OLD ROW AS orow 
  FOR EACH ROW
    WHEN (orow.time_slot_id NOT IN (
          SELECT time_slot_id 
          FROM time_slot)/*last tuple for time_slot_id deleted from time_slot */
        AND orow.time_slot_id IN (
          SELECT time_slot_id
          FROM section)) /* and time slot id still referenced from section*/
    BEGIN
      rollback 
    END;
```

* Place a reorder when inventory falls below a specified minimum

```sql
CREATE TRIGGER reorder AFTER UPDATE OF level ON inventory 
  REFERENCING OLD ROW AS orow, NEW ROW AS nrow
  FOR EACH ROW
    WHEN nrow.level <= (SELECT level
                        FROM minlevel
                        WHERE minlevel.item = orow.item)
    AND orow.level > (SELECT level
                      FROM minlevel
                      WHERE minlevel.item = orow.item)
    BEGIN ATOMIC
      INSERT INTO orders
        (SELECT item, amount 
        FROM reorder
        WHERE reorder.item = orow.item);
    END;
```

### Statement level triggers

* Instead of executing a separate action for each affected row, a single action can be executed for all rows affected by a transaction.
  * Instead of <code>FOR EACH ROW</code>, use <code>FOR EACH STATEMENT</code>.
  * Instead of <code>REFERENCING [OLD/NEW] ROW AS ...</code>, use <code>REFERENCING [OLD/NEW] TABLE AS ...</code> to refer to temporary tables (called <strong>transition tables</strong>) containing all the affected rows.
* Statement level triggers can be more efficient when dealing with SQL statements that update a large number of rows.

## When Not To Use Triggers

* Earlier, triggers were used for tasks such as
  * Maintaining <strong>summary data</strong> (e.g., total salary of each department).
  * Replicating databases by recording changes to special relations (called <strong>change</strong> or <strong>delta relations</strong>), and having a separate process that applies the changes over to a replica
* However, there are better ways of doing these nowadays.
  * Databases today provide built-in <strong>materialized view</strong> facilities to maintain summary data.
  * Databases provide built-in support for replication.
  * Encapsulation facilities can be used instead of triggers in many cases. Define methods to update fields, which directly carry out actions as part of the update methods instead of through a trigger indirectly.


### Risks of Triggers

There are some risks for using triggers. For example,
* Risk of unintended execution of triggers. For example, when
  * Loading data from a backup copy, or Replicating updates at a remote site
    * In this case, the triggered action has already been executed and reflected in the backup or replica in the past $\Rightarrow$ redundant
    * Thus triggers would have to be disabled initially and enabled when the backup site takes over processing from the primary system.
* Trigger error at runtime might lead to the failure of critical transactions that set off the trigger.
* Cascading execution of triggers.
  * e.g., an insert trigger on a relation causes another insert on the same relation
  * might lead to an infinite chain of triggering

<br>

# Recursive Queries

## <code>WITH RECURSIVE</code> clause

* <strong>Recursive views</strong> make it possible to write queries that cannot be written without recursion or iteration.
  * Without recursion, a non-recursive non-iterative program can perform only a fixed number of joins of relation with itself
    * This program can give only a fixed number of levels of prerequisites
    * Given a fixed non-recursive query, we can construct a database with a greater number of levels of prerequisites on which the query will not work
  * Alternatively, we may write a procedure to iterate as many times as required
    * See the procedure <code>findAllPrereqs</code> in the textbook
* Any recursive view must be defined as the union of two subqueries:
  * <strong>base query</strong>: nonrecursive
  * <strong>recursive query</strong>: uses the recursive view.
* The meaning of a recursive view is best understood as follows: 
  1. compute the base query and add all the resultant tuples to the recursively defined view relation (initially empty)
  2. compute the recursive query using the current contents of the view relation, and add all the resulting tuples back to the view relation
  3. keep repeating the above step until no new tuples are added to the view relation. 
  4. at some point, no tuple is added to the view after recursion. This final result is called the <strong>fixed point</strong> of the recursive view definition.
* <strong>Example.</strong> Find all (direct or indirect) prerequisite subjects of each course

  ```sql
  WITH RECURSIVE rec_prereq(course_id, prereq_id) AS
  (
      (
          SELECT  course_id,
                  prereq_id
          FROM    prereq
      )
      UNION
      (
          SELECT  rec_prereq.course_id,
                  prereq.prereq_id
          FROM    rec_prereq,
                  prereq
          WHERE   rec_prereq.prereq_id = prereq.course_id
      )
  )
  SELECT  course_id
  FROM    rec_prereq;
  ```
  * The example view <code>rec_prereq</code> is called the <strong>transitive closure</strong> of the <code>prereq</code> relation, meaning that it is a relation that contains all pairs <code>(cid, pre)</code> such that <code>pre</code> is a direct or indirect prerequisite of <code>cid</code>.
* Recursive views are required to be <strong>monotonic</strong>.
  * For each step of the recursion, the view must contain all of the tuples it contained in the previous step, plus possibly some more.
  * Recursive queries may not use any of the following construct:
    * Aggregation on the recursive view
    * <code>NOT EXISTS</code> on a subquery that uses the recursive view.
    * Set difference (<code>EXCEPT</code>) whose RHS uses the recursive view.

<br>

# Advanced Aggregation Features

The aggregation support in SQL is quite powerful and handles most common tasks with ease.

## Ranking

* Finding the position of a value in a larger set is a common operation. In SQL, <strong>ranking</strong> is done by <code>RANK() OVER</code> in conjunction with an <code>ORDER BY</code> specification.

* <strong>Examples</strong>

  * Find the rank of each student

    ```sql
    SELECT  ID,
            RANK() OVER (ORDER BY (GPA) DESC) AS s_rank
    FROM    student_grades;
    /* student_grades(ID, GPA) is a relation 
    * giving the GPA of each student */
    ```

  * Here, an extra <code>ORDER BY</code> clause can return the results in sorted order.

    ```sql
    SELECT  ID,
            RANK() OVER (ORDER BY (GPA) DESC) AS s_rank
    FROM    student_grades
    ORDER BY s_rank ASC;
    ```

* A basic issue with ranking is how to deal with the case of mutiple tuples that are the same values on the ordering attribute(s).
  * Naturally, the <code>RANK()</code> function leaves gaps. <br>
    Meaning that if the highest GPA is shared by two students, both would get rank <code>1</code>, and the next rank would be <code>3</code>.
  * There is a <code>DENSE_RANK()</code> function which does not leave gaps.

### Ranking with partitions

* Ranking can be done within partitions of data, using <code>PARTITION BY</code>.
* <strong>Examples</strong>
  * Find the rank of students within each department

    ```sql
    SELECT  ID,
            dept_name,
            RANK() OVER 
            (
                PARTITION BY dept_name
                ORDER BY GPA DESC
            ) AS dept_rank
    FROM    dept_grades
    ORDER BY dept_name, dept_rank;
    ```
* Multiple rank expressions can be used within a single <code>SELECT</code> statement.
* When ranking (possibly with partitioning) occurs along with a <code>GROUP BY</code> clause, the <code>GROUP BY</code> clause is applied first, and partitioning and ranking are done on the results of <code>GROUP BY</code>, allowing aggregate values to be used for ranking.

### Other ranking related features

* Several other functions can be used in place of <code>RANK</code>
  * <code>PERCENT_RANK</code>: Gives the rank of the tuple as a fraction
  * <code>CUME_DIST</code>: Cumulative distribution
  * <code>ROW_NUMBER</code>: Sorts the rows and gives each row a unique number corresponding to its position
    * Non-deterministic in presence of duplicates
  * <code>NULLS FIRST</code>, <code>NULLS LAST</code>
  * <code>NTILE(n)</code>: Takes the tuples in each partition in the specified order, and divides them into <code>n</code> buckets with <strong>equal numbers of tuples</strong>

<strong>Example</strong>

* Find for each student the quartile they belong to

  ```sql
  SELECT  ID,
          NTILE(4) OVER (ORDER BY GPA DESC) AS quartile
  FROM    student_grades;
  ```

* Find the rank of each student

    ```sql
    SELECT  ID,
            RANK() OVER (ORDER BY (GPA) DESC NULLS LAST) AS s_rank
    FROM    student_grades;
    /* student_grades(ID, GPA) is a relation 
    * giving the GPA of each student */
    ```

<br>

## Windowing

* Window queries compute an aggregate function over ranges of tuples. They are used to <strong>smooth out</strong> random variables.
* Unlike partitions, windows may overlap, in which case a tuple may contribute to more than one window.
* SQL provides a windowing feature to support such queries.

  ```sql
  ROWS BETWEEN n_1 PRECEDING AND n_2 FOLLOWING
  ```
* <strong>Example</strong>
  * <em>Moving average</em>: given sales values for each date, calculate for each date the average of the sales on that day, the previous day, and the next day

  ```sql
  SELECT  date,
          SUM(value) OVER
          (
              ORDER BY date
              ROWS BETWEEN 1 PRECEDING AND 1 FOLLOWING
          )
  FROM    sales;
  ```
  * the average of total number of credits taken by students in each 3 year.

  ```sql
  SELECT  year
          AVG (num_credits) OVER
          (
              ORDER BY year
              ROWS 3 PRECEDING
          )
  FROM    tot_credits;
  ```
  * the average of total number of credits taken by students in all prior years

  ```sql
  SELECT  year
          AVG (num_credits) OVER
          (
              ORDER BY year
              ROWS UNBOUNDED PRECEDING
          )
  FROM    tot_credits;
  ```

### Windowing with partitions

* SQL supports windowing within partitions.

<strong>Example</strong>

* Find total balance of each account after each transaction on the account

```sql
SELECT  account_number,
        date_time,
        SUM(value) OVER
        (
            PARTITION BY account_number
            ORDER BY date_time
            ROWS UNBOUNDED PRECEDING
        ) AS balance
FROM    transaction
ORDER BY account_number, date_time;
```

### Other windowing related features

* <code>UNBOUNDED</code>: The number of preceding / following rows are unbounded
* <code>CURRENT ROW</code>: Specifies the current row 
  * e.g., <code>ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW</code>
* <code>RANGE BETWEEN ...</code>: Cover all tuples with a particular value rather than covering a specific number of tuples



<br><br><br>

# Reference
**[1]** [Silberschatz, Abraham, Henry F. Korth, and Shashank Sudarshan. "Database system concepts." (2011).](https://db-book.com/) <br>