---
title:  "[DB] Relational Language" 
excerpt: ""
categories:
  - Database System
tags:
  - [Computer Science, DB]
header:
  teaser: "/assets/images/database-system/relational-model/example-of-relation.png"

toc: true
toc_sticky: true

date: 2024-02-29
last_modified_at: 2024-02-29

lang: en
ref: rellang
use_math: true
---

The relational model is the primary data model for commercial data-processing applications. It attained its primary position because of its simplicity, which eases the job of the programmer, compared to earlier data models such as the network model or the hierarchical model. 

# Structure of Relational Databases

A relational database consists of a collection of <strong>tables</strong>, each of which is assigned a unique name. In general, a row in a table represents a relationship among a set of values.

* In the relational model, the term <strong>relation</strong> is used to refer to a table, whilst the term <strong>tuple</strong> is used to refer to a row.
* Similarly, the term <strong>attribute</strong> refers to a column of a table.
  * Denoted as $A_1, \cdots, A_n$.
* A <strong>relation schema</strong> consists of a list of attributes and their corresponding domains.
  * Denoted as $R=(A_1, \cdots, A_n​)$.
  * e.g. <code>instructor = (ID, name, dept_name, salary)</code>  
* The term <strong>relation instance</strong> refers to a specific instance of relation schema $R$, i.e., containing a specific set of rows.
  * Denoted as $r(R)$.
  * An element $t$ of a relation $r$ is called a <strong>tuple</strong>.

<p align = "center">
<img width="400" alt="image" src="/assets/images/database-system/relational-model/example-of-relation.png">
<figcaption style="text-align: center;">$\mathbf{Fig\ 1.}$ Example of relation instance of an instructor Relation </figcaption>
</p>

* The <strong>domain</strong> of the attribute refers to the set of allowed values for each attribute.
  * e.g. the domain of <code>salary</code> attribute is the set of all possible salary values
  * Attribute values are normally required to be <strong>atomic</strong>, i.e., elements of the domain are considered to be indivisible units and do not allow set values.
  * The special value <strong>null</strong> is a member of every domain, indicating that the value is "unknown".
* Relations are unordered, like sets in mathematics.

<br>

# Keys

We must have a way to specify how tuples within a given relation are distinguished; no two tuples in a relation are allowed to have exactly the same value for all attributes.

Let $K \subseteq R$, where $R$ is a relational schema for the relation $r$.

<ul>
  <li>
    <strong>Superkey</strong><br>
    $K$ is a <em>superkey</em> of $R$ if value for $K$ are sufficient to identify a unique tuple of each possible relation instance $r(R)$.
    <ul>
      <li>i.e., $\forall t_1​,t_2​ \in R : t_1 \neq t_2​ \implies t_1.K \neq t_2​.K$</li>
      <li>e.g. in <code>instructor</code> relation, <code>ID</code> and <code>(ID, name)</code> are superkeys.</li>
    </ul>
  </li>
  <li>
    <strong>Candidate key</strong><br>
    A superkey $K$ is a <em>candidate key</em> if $\lvert K \rvert$ is at its minimum value.
    <ul>
      <li>several distinct sets of attributes could serve as candidate key </li>
      <li>e.g. in <code>instructor</code> relation, <code>ID</code> is candidate key.</li>
    </ul>  
  </li>
  <li>
    <strong>Primary key</strong><br>
    <em>Only one</em> of the candidate keys is selected to be the primary key. 
    <ul>
      <li>Usually the most efficient one in terms of computation cost is chosen.</li>
      <li>Chosen by the database designer with care as the principal means of identifying tuples within a relation.</li>
      <li>Chosen such that its attribute values are never, or are very rarely, changed.</li>
      <li>Also referred to as the <strong>primary key constraints</strong>.</li>
    </ul>  
  </li>
</ul><br>

Next, we consider another type of constraint on the contents of relations. Let $A_i$​ be an attribute of relations $r_i$​.

<ul>
  <li>
    A <strong>foreign-key constraint</strong> from attribute(s) $A_1$​ of relation $r_1$​ to the primary-key $A_2$​ of relation $r_2$​ states that on any database instance, the value of $A_1$​ for each tuple in $r_1$​ must also be the value of $A_2$​ for some tuple in $r_2$​.
    <ul>
      <li>i.e., $\forall t_1 \in R_1 ​: \exists t_2​ \in R_2 \quad \text{ s.t. } \quad t_1​.A_1​=t_2​.A_2$​</li>
      <li><em>Motivation</em><br>
        Consider the attribute <code>dept_name</code> of the <code>instructor</code> relation. It would not make sense for a tuple in <code>instructor</code> to have a value for <code>dept_name</code> that does not correspond to <code>department</code> in the department relation. 
      ​</li>
    </ul>
  </li>
  <li>
    Attribute set $A_1$​ is called <strong>foreign key</strong> from $r_1$​, referencing $r_2$​.
    <ul>
      <li>$r_1$: <strong>referencing relation</strong>; </li>
      <li>$r_2$: <strong>referenced relation</strong>; </li>
    </ul>  
  </li>
</ul>

A database schema, along with <em>primary key</em> and <em>foreign-key constraints</em>, can be depicted by <strong>schema diagrams</strong>.

<p align = "center">
<img width="700" alt="image" src="/assets/images/database-system/relational-model/schema-diagram.png">
<figcaption style="text-align: center;">$\mathbf{Fig\ 2.}$ Schema Diagram for University Database</figcaption>
</p>

<br>

# Relational Query Languages

A <strong>query language</strong> is a language in which a user requests information from the database. Query languages can be categorized as:

<ul>
  <li><strong>Imperative query language</strong>
    <ul>
      <li>the user instructs the system to perform a specific sequence of operations on the database to compute the desired result</li>
    </ul>
  </li>
  <li><strong>Functional (Procedural) query language</strong>
    <ul>
      <li>the computation is expressed as the evaluation of functions that may operate on data in the database or on the results of other functions; functions are side-effect free, and they do not update the program state.</li>
      <li>it can be also referred to as <strong>procedural query language</strong>, languages based on <a href="https://en.wikipedia.org/wiki/Procedure">procedure</a> (function) invocations. However, the term is also widely used to refer to imperative languages. </li>
    </ul>
  </li>
  <li><strong>Declarative query language</strong>
    <ul>
      <li>the user describes the desired information without giving a specific sequence of steps or function calls for obtaining that information.</li>
    </ul>
  </li>
</ul>

# Relational Algebra

The <strong>relational algebra</strong> is a procedural query language consisting of a set of operations that take one or two relations as input and produce a new relation as their result.

<ul>
  <li>Six basic operators
    <ul>
      <li>select: $\sigma$</li>
      <li>project: $\Pi$</li>
      <li>union: $\cup$</li>
      <li>set difference: $-$</li>
      <li>Cartesian product: $\times$</li>
      <li>rename: $\rho$</li>
    </ul>
  </li>
</ul>  

## 1. Select $\sigma$

The <strong>select operation</strong> selects tuples that satisfy a given <em>predicate</em>

<ul>
  <li>Notation: $\sigma_p(r)$
    <ul>
      <li>$p$ is called the <strong>selection predicate</strong></li>
      <li>e.g., select those tuples of the instructor relation where the instructor is in the “Physics” department</li>
      <li>Query: $\sigma_{\textrm{dept_name=“Physics”}}(\textrm{instructor})$ </li>
      <li>Result: 
        <p align = "center">
        <img width="300" alt="image" src="/assets/images/database-system/relational-model/select-result.png">
        </p>
      </li>
    </ul>
  </li>
  <li>Comparisons uses $=$, $\neq$, $>$, $\geq$, $<$, $\leq$ in the selection predicate.</li>
  <li>Predicates are combined using connectives; $\wedge$, $\vee$, $\neg$.</li>
  <li>The select predicate can also include comparisons between two different attributes.</li>
</ul>

## 2. Projection $\Pi$

The (unary) <strong>projection operator</strong> $\Pi$ returns its argument relation, with certain attributes removed.

<ul>
  <li>Notation: $\Pi_{A_1, \cdots, A_k}(r)$, where $A_i$'s are attribute names and $r$ is a relation name.
    <ul>
      <li>e.g., eliminate the dept_name attribute of instructor</li>
      <li>Query: $\Pi_{\textrm{ID},\textrm{name},\textrm{salary}}​(\textrm{Instructor})$</li>
      <li>Result: 
        <p align = "center">
        <img width="250" alt="image" src="/assets/images/database-system/relational-model/proj-result.png">
        </p>
      </li>
    </ul>
  </li>
  <li>The result is defined as the relation of $k$ columns obtained by erasing the columns that are not listed.</li>
  <li>Duplicate rows removed from the result, since relations are sets.</li>
</ul>

## 3. Cartesian Product $\times$

The (binary) <strong>Cartesian operator</strong> $\times$ combines information from any two relations. As a result, it constructs a tuple of the result out of each possible pair of tuple from each relation and one from the teaches relation. 

<ul>
  <li>Notation: $r_1 \times r_2$, where $r_j$ are relations.
    <ul>
      <li>e.g. the Cartesian product of the relations instructor and teaches</li>
      <li>Query: $\textrm{Instructor} \times \textrm{Teaches}$</li>
      <li>Result: 
        <p align = "center">
        <img width="400" alt="image" src="/assets/images/database-system/relational-model/prod-result.png">
        </p>
      </li>
      <li>To distinguish between attribute appears in both relations, attach  the name of the source relation to the name of the attribute
        <ul>
          <li>e.g. $\textrm{Instructor}.\textrm{ID}$, $\textrm{Teaches}.\textrm{ID}$</li>
        </ul>
      </li>
    </ul>
  </li>
  <li>Duplicate rows removed from the result, since relations are sets.</li>
</ul>

### Join $\bowtie$

The Cartesian product $r_1 \times r_2$ associates every tuple of $r_1$ with every tuple of $r_2$. Hence, most of the resulting rows have the redundant information. To get only desired tuples that pertain to our predicate $p$, we may write

$$
\sigma_p (r_1 \times r_2)
$$

The composition of a cartesian product followed by a selection operation is called <strong>join operator</strong> $\bowtie$ defined as follows:

$$
r \bowtie_p s = \sigma_p (r \times s)
$$

<ul>
  <li>e.g. $\textrm{Instructor} \bowtie_{\textrm{Instructor}.\textrm{ID} = \textrm{Teaches}.\textrm{ID}} \textrm{Teaches}$</li>
  <li>Result: 
    <p align = "center">
    <img width="500" alt="image" src="/assets/images/database-system/relational-model/join-result.png">
    </p>
  </li>
  <li>It returns only those tuples that satisfy some predicate $p$, among all possible combinations of tuples from the two relations.</li>
  <li><u><em>It does not belong to the six basic operations.</em></u></li>
</ul>

## 4. Union $\cup$
 
The (binary) <strong>union operator</strong> $\cup$ combines two relations and returns all tuples that are present in at least one of the relations. Two relations must satisfy the condition.

<ul>
  <li>Notation: $r_1 \cup r_2$, where $r_j$ are relations.
    <ul>
      <li>e.g. find all courses taught in the Fall 2017 semester, or in the Spring 2018 semester, or in both</li>
      <li>Query: $\prod_{\textrm{courseID}} (\sigma_{\textrm{semester}=\textrm{"Fall"} \wedge \textrm{year}=2017} (\textrm{Section})) \cup \prod_{\textrm{courseID}} (\sigma_{\textrm{semester}=\textrm{"Spring"} \wedge \textrm{year}=2018} (\textrm{Section}))$</li>
    </ul>
  </li>
</ul>

Two relations must satisfy the conditions to be valid: 

<ul>
  <li>$r_1$ and $r_2$ must have the same <strong>arity</strong> (same number of attributes)</li>
  <li>The attribute domains must be compatible (e.g., the 2nd column of $r_1$ deals with the same type of values as does the 2nd column of $r_2$)</li>
</ul>

### Intersection $\cap$

The (binary) <strong>intersection operator</strong> $\cap$ combines two relations and returns all tuples that are present in both the relations. 

<ul>
  <li>Notation: $r_1 \cap r_2$, where $r_j$ are relations.
    <ul>
      <li>e.g. find all courses taught in both the Fall 2017 semester and the Spring 2018 semester</li>
      <li>Query: $\prod_{\textrm{courseID}} (\sigma_{\textrm{semester}=\textrm{"Fall"} \wedge \textrm{year}=2017} (\textrm{Section})) \cap \prod_{\textrm{courseID}} (\sigma_{\textrm{semester}=\textrm{"Spring"} \wedge \textrm{year}=2018} (\textrm{Section}))$</li>
    </ul>
  </li>
</ul>

Two relations must satisfy the conditions to be valid: 

<ul>
  <li>$r_1$ and $r_2$ must have the same <strong>arity</strong> (same number of attributes)</li>
  <li>The attribute domains must be compatible (e.g., the 2nd column of $r_1$ deals with the same type of values as does the 2nd column of $r_2$)</li>
</ul>

## 5. Set-Difference $-$

The (binary) <strong>set-difference operator</strong> $-$ combines two relations and returns all tuples that are in one relation but are not in another.

<ul>
  <li>Notation: $r_1 - r_2$, where $r_j$ are relations.
    <ul>
      <li>e.g. find all courses taught in the Fall 2017 semester, but not in the Spring 2018 semester</li>
      <li>Query: $\prod_{\textrm{courseID}} (\sigma_{\textrm{semester}=\textrm{"Fall"} \wedge \textrm{year}=2017} (\textrm{Section})) - \prod_{\textrm{courseID}} (\sigma_{\textrm{semester}=\textrm{"Spring"} \wedge \textrm{year}=2018} (\textrm{Section}))$</li>
    </ul>
  </li>
</ul>

Two relations must satisfy the conditions to be valid: 

<ul>
  <li>$r_1$ and $r_2$ must have the same <strong>arity</strong> (same number of attributes)</li>
  <li>The attribute domains must be compatible (e.g., the 2nd column of $r_1$ deals with the same type of values as does the 2nd column of $r_2$)</li>
</ul>

## Assignment $\leftarrow$

The <strong>assignment operator</strong> $\leftarrow$ assigns a relational algebra expression to a temporary relation variable. It works similar as the assignment operation in programming languages. Note that it does not provide any additional power to the algebra.

<ul>
  <li>Notation: $x \leftarrow E$, where $x$ is a name and $E$ is a relational algebra expression.
    <ul>
      <li>e.g. Find all instructors in any of the Physics or Music departments:
      $$
      \begin{aligned}
        & \textrm{Physics} \leftarrow \sigma_{\textrm{dept_name} = \textrm{"Physics"}} (\textrm{Instructor}) \\
        & \textrm{Music} \leftarrow \sigma_{\textrm{dept_name} = \textrm{"Music"}} (\textrm{Instructor}) \\
        & \textrm{Physics} \cup \textrm{Music} \\
      \end{aligned}
      $$
      </li>
    </ul>
  </li>
  <li>With the assignment operation, a query can be written as a sequential program consisting of a series of assignments followed by an expression whose value is displayed as the result of the query.</li>
</ul>

## 6. Rename $\rho$

Relations have their name, whereas the results of relational-algebra expressions do not; it is useful to be able to give them names. The (unary) <strong>rename operator</strong> $\rho$ assigns a name to the results of relational algebra expressions, and returns the result of the expression $E$ under the given name $x$, and with the attributes renamed to $A_1, \cdots, A_n$.

<ul>
  <li>Notation: $\rho_{x(A_1​,\cdots,A_k​)}​(E)$ where $x$ is a name, $A_i$'s are attributes, and $E$ is a relational algebra expression.
    <ul>
      <li>e.g. Find the ID and name of those instructors who earn more than the instructor whose ID is 12121.</li>
      <li>Query: $\prod_{i.\textrm{ID}, i.\textrm{name}} ( \sigma_{i.\textrm{salary} > w.\textrm{salary}} (\rho_i (\textrm{Instructor}) \times \sigma_{w.\textrm{ID} = 12121} (\rho_w (\textrm{Instructor}))) )$</li>
    </ul>
  </li>
  <li>Rename compared with Assignment
    <ul> 
      <li>Naming a relation: this function can be used to give a name to a result relation; therefore, in this aspect, the rename operation may be equivalent to the assignment operation</li>
      <li>Renaming a relation and its attributes: the rename operation can change the names of attributes, whereas the assignment operation cannot</li>
    </ul>
  </li>
</ul><br>

## Remarks

<ul>
  <li>
    <strong>Composition of Relational Operations</strong><br>
    The result of a relational-algebra operation is a relation, and therefore relational-algebra operations can be composed together into a relational-algebra expression.
    <ul>
      <li>e.g. find the names of all instructors in the “Physics” department</li>
      <li>Query: $\Pi_{\textrm{dept_name}} (\sigma_{\textrm{dept_name}=\textrm{"Physics"}} (\textrm{Instructor}))$</li>
    </ul>
  </li>
  <li>
    <strong>Equivalent Queries</strong><br>
    There is more than one way to write a query in relational algebra.
    <ul>
      <li>e.g., find information about courses taught by instructors in the Physics department with a salary greater than 90,000
        <ul>
          <li><span style="color:#ff5723"><strong>Query 1:</strong> $\sigma_{\textrm{dept_name}=\textrm{"Physics"} \wedge \textrm{Salary} > 90000} (\textrm{Instructors}) $</span></li>
          <li><span style="color:#ff5723"><strong>Query 2:</strong> $\sigma_{\textrm{dept_name}=\textrm{"Physics"}} ( \sigma_{\textrm{Salary} > 90000} (\textrm{Instructors}) )$</span></li>
        </ul>
      </li>
      <li>e.g., find information about courses taught by instructors in the Physics department
        <ul>
          <li><span style="color:#ff5723"><strong>Query 1:</strong> $\sigma_{\textrm{dept_name}=\textrm{"Physics"}} (\textrm{Instructor} \bowtie_{\textrm{Instructor}.\textrm{ID} = \textrm{Teaches}.\textrm{ID}} (\textrm{Teaches}) )$</span></li>
          <li><span style="color:#ff5723"><strong>Query 2:</strong> $(\sigma_{\textrm{dept_name}=\textrm{"Physics"}} (\textrm{Instructor})) \bowtie_{\textrm{Instructor}.\textrm{ID} = \textrm{Teaches}.\textrm{ID}} \textrm{Teaches}$</span></li>
        </ul>
      </li>
    </ul>  
  </li>
</ul><br>


<br><br><br>

# Reference
**[1]** [Silberschatz, Abraham, Henry F. Korth, and Shashank Sudarshan. "Database system concepts." (2011).](https://db-book.com/) <br>
