---
title:  "[DB] Normalization" 
excerpt: ""
categories:
  - database-system
tags:
  - [Computer Science, DB]
header:
  teaser: "/assets/images/database-system/teaser/normalization.png"

toc: true
toc_sticky: true

date: 2024-04-22
last_modified_at: 2024-04-22

lang: en
ref: normal
use_math: true
---

In general, the goal of relational database design is to generate a set of relation schemas that allows us to store information without unnecessary redundancy, yet also allows us to retrieve information easily. This is accomplished by designing schemas that are in an appropriate <strong>normal form</strong>. 

This post introduces a formal approach to relational database design based on the notion of functional dependencies. We then define normal forms in terms of functional dependencies and other types of data dependencies. 

# Overview of Normalization

Suppose that the tables <code>instructor</code> and <code>department</code> are combined via natural join.

<p align="center">
<img width="400" alt="Redundant Relational Designs" src="/assets/images/database-system/normalization/redundant.png">
<figcaption style="text-align: center;">$\mathbf{Fig\ 1.}$ Redundant Relational Designs</figcaption>
</p>

This relation design is not good because:

* There are repetitions of information.
* <code>NULL</code> values must be used when a new <code>department</code> with no <code>instructor</code> is added.

However, not all combined schemas result in repetitions of information.

## Decomposition

The only way to avoid the repetition-of-information problem in the previous example is to decompose it into two schemas: <code>instructor</code> and <code>department</code>. However, not all decompositions are helpful. Consider the decomposition

<table style="width:90%;border-collapse:collapse;border-spacing:0;margin-left:auto;margin-right:auto; auto" class="center"><thead><tr><th style="border-color:inherit;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;font-weight:normal;overflow:hidden;padding:10px 5px;text-align:center;vertical-align:top;word-break:normal"><span style="font-weight:bold">Relation</span></th><th style="border-color:inherit;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;font-weight:normal;overflow:hidden;padding:10px 5px;text-align:center;vertical-align:top;word-break:normal"><span style="font-weight:bold">Attributes</span></th></tr></thead><tbody><tr><td style="border-color:inherit;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:center;vertical-align:top;word-break:normal">employee</td><td style="border-color:inherit;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:center;vertical-align:top;word-break:normal">ID, name, street, city, salary</td></tr></tbody></table>

into the following two schemas:

<table style="width:90%;border-collapse:collapse;border-spacing:0;margin-left:auto;margin-right:auto; auto" class="center"><thead><tr><th style="border-color:inherit;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;font-weight:normal;overflow:hidden;padding:10px 5px;text-align:center;vertical-align:top;word-break:normal"><span style="font-weight:bold">Relation</span></th><th style="border-color:inherit;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;font-weight:normal;overflow:hidden;padding:10px 5px;text-align:center;vertical-align:top;word-break:normal"><span style="font-weight:bold">Attributes</span></th></tr></thead><tbody><tr><td style="border-color:inherit;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:center;vertical-align:top;word-break:normal">employee1</td><td style="border-color:inherit;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:center;vertical-align:top;word-break:normal">ID, name</td></tr><tr><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">employee2</td><td style="border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">name, street, city, salary</td></tr></tbody></table>

The problem arises when there are multiple employees with the same name.

<p align="center">
<img width="600" alt="Bad decomposition" src="/assets/images/database-system/normalization/bad-decomposition.png">
<figcaption style="text-align: center;">$\mathbf{Fig\ 2.}$ We cannot reconstruct the original employee relation; so, this is a <strong>lossy</strong> decomposition. </figcaption>
</p>

We can indicate that a certain street, city, and salary pertain to someone named Kim, but we are unable to distinguish which of the Kims. Such decomposition is unable to represent certain important facts about the relation before the decomposition. This kind of decomposition is referred to as a <strong>lossy decomposition</strong>. Conversely, those that are not are referred to as <strong>lossless decomposition</strong>.

## Lossless Decomposition

The formal definitions of lossy/lossless decomposition are given as follows:

<div style="border:1px solid blue; padding:10px;">

$\color{blue}{\mathbf{Definition.}}$ <span style="color:blue">Lossy and Lossless Decomposition</span><br>

Let $r$ be a relation, $R$ be a relation schema of $r$ and let $R_1$, $R_2$ form a decomposition of $R$. In other words, $R = R_1 \cup R_2$. The decomposition is said to be <strong>lossless</strong> if there is no loss of information by replacing $R$ with $R_1 \cup R_2$. Equivalently,

$$
\Pi_{R_1} (r) \bowtie \Pi_{R_2} (r) = r
$$

Conversely, a decomposition is said to be <strong>lossy</strong> if

$$
r \subsetneq \Pi_{R_1} (r) \bowtie \Pi_{R_2} (r)
$$

<br>

<p align="center">
<img width="400" alt="Definition Visualization" src="/assets/images/database-system/normalization/definition-visualization.png">
<figcaption style="text-align: center;">$\mathbf{Fig\ 2.}$ The visualization of definition </figcaption>
</p>

</div><br>


And <strong>functional dependencies</strong>, discussed in the following section, can be used to show when certain decompositions are lossless. Consider the decomposition of $R$ into $R_1$​, $R_2$​. The sufficient condition for it to be a <strong>lossless decomposition</strong> is that <em>at least one</em> of the following dependencies is in $F^+$:

* $R_1 \cap R_2​ \to R_1$​
* $R_1 \cap R_2​ \to R_2$​


## Normalization Theory

We are now in a position to define a general methodology for deriving a set of schemas each of which is in <em>good form</em>; that is, does not suffer from the repetition-of-information problem. The method for designing a relational database is to use a process commonly known as <strong>normalization</strong>. The goal is to generate a set of relation schemas $$\{R_1, \cdots, R_n\}$$ that allows us to store information of relation $R$ without unnecessary redundancy, yet also allows us to retrieve information easily.

<br>

# Functional Dependency

There are usually a variety of constraints on the data in the real world. For example,

* Students and instructors are uniquely identified by their ID;
* Each student and instructor has only one name;

An instance of a relation that satisfies all such real-world constraints is called a <strong>legal instance</strong> of the relation; a legal instance of a database is one where all the relation instances are legal instances. Some of the most commonly used types of real-world constraints can be represented formally as <strong>keys</strong> (superkeys, candidate keys, and primary keys), or as <strong>functional dependencies</strong>. A functional dependency is a generalization of the notion of a key. 

## Definition

<div style="border:1px solid blue; padding:10px;">

$\color{blue}{\mathbf{Definition.}}$ <span style="color:blue">Functional Dependency</span><br>

Consider a relation schema $r(R)$, and let $\alpha, \beta \subseteq R$ be sets of attributes. Given an instance of $r(R)$, the instance is said to satisfy a <strong>functional dependency</strong> $\alpha \to \beta$ if for all pairs of tuples $t_1$​ and $t_2$​ in the instance: 

$$
t_1​[\alpha]=t_2​[\alpha] \implies t_1​[\beta]=t_2​[\beta].
$$

The functional dependency $F$ is said to <strong>hold</strong> on schema $r(R)$ if every legal instance of $r(R)$ satisfies the functional dependency. And a functional dependency is referred to as <strong>trivial</strong> if it is satisfied by all instances of a relation.

In general, $\alpha \to \beta$ is trivial if $\beta \subseteq \alpha$.

</div><br>

Note that a specific instance of a relation schema may satisfy a functional dependency even if the functional dependency does not hold on all legal instances.

* e.g., A specific instance of <code>instructor</code> may, by chance, satisfy <code>name → ID</code>

<br>

One easy way to understand the concept of functional dependency is to interpret $\alpha \to \beta$ as:

> If values of $\alpha$ is given, then values of $\beta$ is uniquely determined.

## Keys and Functional Dependencies

Functional dependencies allow us to express constraints that cannot be expressed using superkeys.

* $K$ is a <strong>superkey</strong> for relational schema $r(R)$ if and only if the functional dependency $K \to R$ holds on $r(R)$.
* $K$ is a <strong>candidate key</strong> for $r(R)$ if and only if the functional dependency $K \to R$ holds on $r(R)$, and for any $L \subsetneq K$, $L \not\to R$.

For example, 

* <code>in_dep (<strong>ID</strong>, name, salary, <strong>dept_name</strong>, building, budget)</code>.
* We expect these functional dependencies to hold:
    * <code>dept_name → budget</code> because for each department there is a unique budget amount.
    * <code>ID → building</code>
* but would not expect the following to hold: 
    * <code>dept_name → salary</code>

## Use of Functional Dependencies

Functional dependencies can be used to:

* Test relations to see if they are legal under a given set of functional dependencies
* Specify constraints on the set of legal relations

<br>

# Functional Dependency Theory

## Closure

Given a set $F$ of functional dependencies, there are certain other functional dependencies that are <strong>logically implied</strong> by $F$.

The set of all functional dependencies logically implied by $F$ is called the <strong>closure</strong> of $F$, and is denoted as $F^+$.

<div style="border:1px solid blue; padding:10px;">

$\color{blue}{\mathbf{Definition.}}$ <span style="color:blue">Closure of a Set of Functional Dependencies</span><br>

Given a set $F$ of functional dependencies, there are certain other functional dependencies that are logically implied by $F$. For example, if $A \to B$ and $B \to C$, then we can infer that $A \to C$. The set of all functional dependencies logically implied by $F$ is the <strong>closure</strong> of $F$. We denote the closure of $F$ by $F^+$. Hence, all of functional dependencies in $F$ are contained in $F^+$.

</div><br>

### Armstrong's Axioms

For any set $F$ of functional dependencies, the closure $F^+$ of $F$ can be obtained by repeatedly applying <strong>Armstrong’s axioms</strong>.

<div style="border:1px solid blue; padding:10px;">

$\color{blue}{\mathbf{Definition.}}$ <span style="color:blue">Armstrong's Axioms</span><br>

Let $\alpha$, $\beta$, $\gamma$, and $\delta$ be sets of attributes of a relation schema $R$ with the set of functional dependency $F$.

<ul>
  <li><strong style="color:blue">Reflexive rule</strong>: $\beta \subseteq \alpha \implies \alpha \to \beta$ </li>
  <li><strong style="color:blue">Augmentation rule</strong>: $\alpha \to \beta \implies \forall \gamma: \gamma \alpha \to \gamma \beta$ </li>
  <li><strong style="color:blue">Transitivity rule</strong>: $\alpha \to \beta \wedge \beta \to \gamma \implies \alpha \to \gamma$</li>
</ul>

</div><br>

Note that these rules are both

<ul>
  <li><strong>Sound</strong>: generating only functional dependencies that actually hold</li>
  <li><strong>Complete</strong>: generating all functional dependencies that hold</li>
</ul>

Although Armstrong’s axioms are complete, it is tiresome to use them directly for the computation of $F^+$. To simplify matters further, we list additional rules.

<div style="border:1px solid #bf1100; padding:10px;">

$\color{#bf1100}{\mathbf{Remark.}}$ <span style="color:#bf1100">Addtional rules</span><br>

Some additional rules can be inferred from Armstrong’s axioms:

<ul>
  <li><strong>Union rule</strong>: $\alpha \to \beta \wedge \alpha \to \gamma \implies \alpha \to \beta \gamma$ </li>
  <li><strong>Decomposition rule</strong>: $\alpha \to \beta \gamma \implies \alpha \to \beta \wedge \alpha \to \gamma$ </li>
  <li><strong>Pseudo-transitivity rule</strong>: $\alpha \to \beta \wedge \gamma\beta \to \delta \implies \alpha \gamma \to \delta$</li>
</ul>

</div><br>

The following pseudocode shows a procedure that demonstrates formally how to use Armstrong’s axioms to compute $F^+$:

```sql
F_closure = F
REPEAT
  FOR EACH f IN F_closure
    APPLY reflexivity AND augmentation ON f
    ADD TO F_closure
  FOR EACH PAIR f1, f2 IN F_closure
    IF f1 AND f2 APPLY transitivity
    THEN ADD TO F_closure
UNTIL (F_closure CONVERGE)
```

  * Note that the LHS & RHS of a functional dependency are both subsets of $R$. Since a set of size $n$ has $2^n$ subsets, there are a total of $2^n \times 2^n = 2^{2n}$ possible functional dependencies, where $n$ is the number of attributes in $R$. Thus, the procedure is guaranteed to terminate, though it may be very lengthy.


### Closure of Attribute Sets

We say that an attribute $\beta$ is <strong>functionally determined</strong> by $\alpha$ if $\alpha \to \beta$.

Given a set of attributes $\alpha$, we can also define the <strong>closure</strong> of $\alpha$ under $F$ (denoted by $\alpha^+$) as the set of attributes that are <strong>functionally determined</strong> by $\alpha$ under $F$. It is obtained by the following procedure:

```sql
A_closure = A
REPEAT
  FOR EACH β -> 𝛄 IN F
    IF β IN A_closure
    THEN ADD 𝛄 TO A_closure
UNTIL (A_closure CONVERGE)
```

<br>

<div style="border:1px solid green; padding:10px;">

$\color{green}{\mathbf{Example.}}$ Suppose we are given a relation schema <code>r(A,B,C,G,H,I)</code> and the set of functional dependencies <code>F = { A → B, A → C, CG → H, CG → I, B → H}</code>. Then, <code>(AG)+</code> is given by <br><br>

<strong style="color:green">1.</strong> <code>result = AG</code> <br>
<strong style="color:green">2.</strong> <code>result = ABCG</code> since <code>A → B</code> and <code>A → C</code> <br>
<strong style="color:green">3.</strong> <code>result = ABCGH</code> since <code>CG → H</code> and <code>CG ⊆ ABCG</code> <br>
<strong style="color:green">4.</strong> <code>result = ABCGHI</code> since <code>CG → I</code> and <code>CG ⊆ ABCGH</code> <br><br>

Then, it can be used for testing if <code>AG</code> is candidate key or not. <br><br>

<strong style="color:green">1.</strong> Is <code>AG</code> a superkey? In other words, <code>AG → R</code> (that is, <code>R = (AG)+</code>)? <br>
<strong style="color:green">2.</strong> Is Is any subset of AG a superkey? <br>

</div><br>

It can be used for various purposes:

* <strong>Testing for superkey</strong>
  * Testing if $\alpha$ is a superkey can be done by checking if $R \subseteq \alpha^+$.
* <strong>Testing functional dependencies</strong>
  * Testing if a functional dependency $\alpha \to \beta$ is in $F^+$ can be done by checking $\beta \subseteq \alpha^+$. 
* <strong>Computing the closure $F^+$</strong>
  * $F^+$ can be obtained by finding $\gamma^+$ for each $\gamma \subseteq R$, and for each $S \subseteq \gamma^+$, yielding a function dependency $\gamma \to S$.

## Canonicial Cover

Whenever a user performs an update on a relation, the database system must ensure that the update does not violate any functional dependencies. (If an update violates any functional dependencies in the set $F$, the system must roll back the update.)

The effort spent in checking for violations in functional dependencies can be reduced by testing on only a simplified set of functional dependencies that has the same closure as the given set. This is the <strong>canonical cover</strong> of the functional dependencies.

### Extraneous Attributes

To define the canonical cover, the <strong>extraneous attributes</strong> must be first defined. When removing an atrribute from a functional dependency: 

* Removing an attribute from the <strong>left</strong> side of a functional dependency could make it a <strong>stronger</strong> constraint.
  * For example, for $AB \to C$ and removing $B$, we get the possibly stronger result $A \to C$
* Removing an attribute from the <strong>right</strong> side of a functional dependency could make it a <strong>weaker</strong> constraint.
  * For example, for $AB \to CD$ and removing $C$, we get the possibly weaker result $AB \to D$

<br>

But, depending on what our set $F$ of functional dependencies happens to be, these discardings can be done safely:

* For example, $$F = \{ AB \to C, A \to D, D \to C \}$$
  * We can show that $F$ logically implies $A \to C$, making $B$ extraneous in $AB \to C$.
* For example, $$F = \{ AB \to CD, A \to C \}$$
  * Even after replacing $AB \to CD$ by $AB \to D$, we can still infer $AB \to C$, thus $AB \to CD$

Hence, for a set $F$ of functional dependencies and a functional dependency $\alpha \to \beta \in F$, the <strong>extraneous attribute</strong> $A$ as follows:

<div style="border:1px solid blue; padding:10px;">

$\color{blue}{\mathbf{Definition.}}$ <span style="color:blue">Extraneous Attributes</span><br>

Consider a set $F$ of functional dependencies and a functional dependency $\alpha \to \beta \in F$. An attribute of functional dependency in $F$ is said to be <strong>extraneous</strong> if it can be removed without changing its closure $F^+$. Formally, attribute $A$ is <strong>extraneous</strong> in $\alpha$ or in $\beta$ if 

<ul>
  <li>if $A \in \alpha$ <br>
  $F$ logically implies $(F - \{ \alpha \to \beta \}) \cup \{ (\alpha - A) \to \beta \}$
  </li>
  <li>if $A \in \beta$ <br>
  $(F - \{ \alpha \to \beta \}) \cup \{ \alpha \to (\beta - A) \}$ logically implies $F$.
  </li>
</ul>

Notice that implication in the opposite direction is trivial in each of the cases above, since a stronger functional dependency always implies a weaker one.

</div><br>

For testing if an attribute $A$ in the functional dependency $\alpha \to \beta \in F$ is extraneous: 

<ul>
  <li><strong>Testing if $A \in \alpha$ extraneous in $\alpha$</strong></li>
  <ol>
    <li>Let $\gamma = \alpha - A$;</li>
    <li>Compute $\gamma^+$ under $F$;</li>
    <li>If $\beta \subseteq \gamma^+$, $A$ is extraneous in $\alpha$;</li>
  </ol>
  <li><strong>Testing if $A \in \beta$ extraneous in $\beta$</strong></li>
  <ol>
    <li>Let $F^\prime = (F - \{\alpha \to \beta\}) \cup \{ \alpha \to (\beta - A) \}$;</li>
    <li>Compute $\alpha^+$ under $F^\prime$;</li>
    <li>If $A \in \alpha^+$, $A$ is extraneous in $\beta$;</li>
  </ol>
</ul><br>

<div style="border:1px solid green; padding:10px;">

$\color{green}{\mathbf{Example.}}$ Suppose we are given a relation schema <code>r(A,B,C,G,H,I)</code> and the set of functional dependencies <code>F = { AB → CD, A → E, E → C}</code>. To check if <code>C</code> is extraneous in  <code>AB → CD</code>, we: 

<ul>
  <li>compute the attribute closure of <code>AB</code> under <code>F' = {AB → D, A → E, E → C}</code>.</li>
  <li>The closure is <code>ABCDE</code>, which includes <code>CD</code>.</li>
  <li>This implies that <code>C</code> is extraneous.</li>
</ul>

</div><br>

### Definition

Having defined the concept of extraneous attributes, we can explain how we can construct a canonical cover:

<div style="border:1px solid blue; padding:10px;">

$\color{blue}{\mathbf{Definition.}}$ <span style="color:blue">Canonical cover</span><br>

A canonical cover for $F$ is a set of dependencies $F_c$​ that satisfies all of the followings:

<ul>
  <li>$F$ logically implies all dependencies in $F_c$​;</li>
  <li>$F_c$​ logically implies all dependencies in $F$;</li>
  <li>None of the attributes in $F_c$​ is extraneous;</li>
  <li>Each left side of all functional dependencies in $F_c$​ is unique;
    <ul>
      <li>i.e., $\forall \alpha_1 \to \beta_1​, \alpha_2​ \to \beta_2 \in F_c : \alpha_1​ \neq \alpha_2$​</li>
    </ul>
  </li>
</ul>

</div><br>
  
### Computing Canonical Covers

To compute a canonical cover for $F$:

```sql
F_c = F
REPEAT
  FOR EACH ɑ -> β1, ɑ -> β2 IN F_c
    APPLY union rule /* ɑ -> β_1, β_2 */
    REPLACE TO F
  FOR EACH f in F_c
    IF EXISTS (extraneous attribute A) IN f
    REMOVE A FROM f
UNTIL (F_c CONVERGE)
```

* Note that the <strong>union rule</strong> may become applicable after some extraneous attributes have been deleted, so it has to be re-applied.
* Since the algorithm permits a choice of any extraneous attribute, it is possible that there may be several possible canonical covers for a given $F$.
  * However, any $F_c$ is minimal in a certain sense — it does not contain extraneous attributes, and it combines functional dependencies with the same LHS.

<br>

<div style="border:1px solid green; padding:10px;">

$\color{green}{\mathbf{Example.}}$ Suppose we are given a relation schema <code>r(A,B,C)</code> and the set of functional dependencies <code>F = { A → BC, B → C, A → B, AB → C}</code>. Then, <code>F_c</code> is given by <br><br>

<strong style="color:green">1.</strong> <code>result = {A → BC, B → C, AB → C}</code> by union rule; <br>
<strong style="color:green">2.</strong> <code>result = {A → BC, B → C}</code> since <code>A</code> is extraneous; <br>
<strong style="color:green">3.</strong> <code>result = {A → B, B → C}</code> since <code>C</code> is extraneous; <br>

</div><br>

## Dependency Preservation

Testing <strong>functional dependency</strong> constraints each time the database is updated can be costly. Thus, it is useful to design the database in a way that constraints can be tested efficiently. 

When decomposing a relation, it may be no longer possible to do the testing without having to perform a Cartesian product of join operations. In particular, if testing a functional dependency can be done by considering just one relation, then the cost of testing this constraint is low. 

A decomposition that makes it computationally hard to enforce functional dependency is said to be not <strong>dependency preserving</strong>.

Conversely, if functional dependencies of the original relation can be checked on the decomposed relations without the need of Cartesian product or join operations, is said to be <strong>dependency preserving</strong>.

<div style="border:1px solid blue; padding:10px;">

$\color{blue}{\mathbf{Definition.}}$ <span style="color:blue">Dependency Preservation</span><br>

Let $F$ be the set of functional dependencies on schema $R$, and let $R_1, \cdots, R_n$ be a decomposition of $R$. Let $F_i$ be a <strong>restriction</strong> of $F$ to $R_i$, i.e. a set of all functional dependencies in $F^+$ that include only attributes of $R_i$.

<br><br>

The decomposition is <strong>dependency preserving</strong> if the following holds:

$$
\left( \bigcup_{i=1}^n F_i \right)^+ = F^+.
$$

</div><br>

### Testing for Dependency Preservation

The following algorithm checks if a dependency $\alpha \to \beta$ is preserved in a decomposition of $R$ into $$D = \{ R_1, \cdots, R_n \}$$.

```sql
result = ɑ
REPEAT
  FOR EACH R[i] IN D
    t = CLOSURE(result INTERSECT R[i]) INTERSECT R[i]
    result = result UNION t
UNTIL (result CONVERGE)
```

This sequence of steps is equivalent to computing the closure of <code>result</code> under $F_i$. 

If result contains all attributes in $\beta$, then the functional dependency $\alpha \to \beta$ is presereved.

This method takes polynomial time, instead of exponential time required when using the definition directly.

<br>

# Multivalued Dependencies (MVDs)

Functional dependencies rule out certain tuples from being in a relation. For example, if $A \to B$, then we can't have two tuples with the same $A$ value but different $B$ values. 

<strong>Multivalued dependency</strong>, however, do not rule out the existence of certain tuples; instead, they require that other tuples of a certain form be present in the relation.

<div style="border:1px solid blue; padding:10px;">

$\color{blue}{\mathbf{Definition.}}$ <span style="color:blue">Multivalue Dependency</span><br>

Let $r(R)$ be a relation schema and let $\alpha \subseteq R$ and $\beta \subseteq R$. The <strong>multivalued dependency</strong>

$$
\alpha \twoheadrightarrow \beta
$$

("$\alpha$ <strong>multidetermines</strong> $\beta$") holds on $R$ if, in any legal instance of relation $r(R)$, for all pairs of tuples $t_1, t_2 \in R$ such that $t_1 [\alpha] = t_2 [\alpha]$, there exists tuples $t_3, t_4 \in R$ that satisfies all of the followings:

<ul>
  <li>$t_1 [\alpha] = t_2 [\alpha] = t_3 [\alpha] = t_4 [\alpha]$</li>
  <li>$t_3 [\beta] = t_1 [\beta]$</li>
  <li>$t_3 [R - \beta] = t_2 [R - \beta]$</li>
  <li>$t_4 [\beta] = t_2 [\beta]$</li>
  <li>$t_4 [R - \beta] = t_1 [R - \beta]$</li>
</ul>

Note that $\alpha \twoheadrightarrow \beta$ is trivial if $\beta \subseteq \alpha$ or $\beta \cup \alpha = R$. And the following is a tabular representation of the multivalued dependency $\alpha \twoheadrightarrow \beta$: <br><br>

<p align="center">
<img width="400" alt="Tabular representation of $\alpha \twoheadrightarrow \beta$" src="/assets/images/database-system/normalization/tabular-representation.png">
<figcaption style="text-align: center;">$\mathbf{Fig\ 2.}$ Tabular representation of $\alpha \twoheadrightarrow \beta$. </figcaption>
</p>

</div><br>

Intuitively, $\alpha \twoheadrightarrow \beta$ says that the relationship between $\alpha$ and $\beta$ is independent of the relationship between $\alpha$ and $R - \beta$. In other words, the attributes $\beta$ and $R - \beta$ are dependent on $\alpha$, but independent of each other. Databases with non-trivial multivalued dependencies thus exhibit redundancy.

Informally, the following alternative definition is also possible:

<div style="border:1px solid blue; padding:10px;">

$\color{blue}{\mathbf{Definition.}}$ <span style="color:blue">Alternative definition of MVD</span><br>

Let $R$ be a relation schema with a set of attributes that are partitioned into 3 nonempty subsets $X$, $Y$, $Z$. Then, $X \twoheadrightarrow Y$ holds on $R$ if any only if for all possible relations $r(R)$:

$$
\begin{aligned}
  & \langle x_1, y_1, z_1 \rangle, \langle x_1, y_2, z_2 \rangle \in r \\
  \implies & \langle x_1, y_2, z_1 \rangle, \langle x_1, y_1, z_2 \rangle \in r
\end{aligned}
$$

Note that since the behavior of $Y$ and $Z$ are identical, it follows that $X \twoheadrightarrow Y \Leftrightarrow X \twoheadrightarrow Z$.

</div><br>


<div style="border:1px solid green; padding:10px;">

$\color{green}{\mathbf{Example.}}$ Consider the schema <code>r (ID, dept_name, street, city)</code> and an example relation on that schema: <br><br>

<p align="center">
<img width="350" alt="An example of redundancy in a relation on a BCNF schema." src="/assets/images/database-system/normalization/mvd-example.png">
<figcaption style="text-align: center;">$\mathbf{Fig\ 2.}$ An example of redundancy in a relation on a BCNF schema. </figcaption>
</p>

We must repeat the <code>dept_name</code> once for each <code>address</code> that an <code>instructor</code> has, and we must repeat the <code>address</code> for each <code>dept_name</code> with which an <code>instructor</code> is associated. This repetition is unnecessary, since the relationship between an <code>instructor</code> and his <code>address</code> is independent of the relationship between that <code>instructor</code> and <code>dept_name</code>. (In other words, <code>ID →→ street, city</code> and <code>ID →→ dept_name</code>)

</div><br>

From the definition of MVDs, we can derive the following rule: 

<div style="border:1px solid red; padding:10px;">

$\color{red}{\mathbf{Thm.}}$ Every functional dependency is also a multivalued dependency:

$$
\alpha \to \beta \implies \alpha \twoheadrightarrow \beta
$$

<details>
<summary style="color:red">$\color{red}{\mathbf{Proof.}}$</summary>
<div markdown="1">

Every FD is an MVD because if $\alpha \to \beta$, then swapping $\beta$'s between tuples that agree on $\alpha$ doesn't create new tuples.

Specifically, suppose $\alpha \to \beta$, and let $t_1 = (a, b, c)$ and $t_2 = (a, d, e)$ are given. By the functional dependency, $b = d$. Therefore, $t_3 = t_1 = (a, d, c)$ and $t_4 = t_2 = (a, b, e)$ naturally exist without considering additional tuples.

$$\tag*{$\blacksquare$}$$

</div>
</details>

</div><br>

<h4>Use of multivalued dependencies</h4>

Multivalued dependencies can be used to:

* Test relations to <strong>determine</strong> whether they are legal under a given set of functional and multivalued dependencies.
* Specify <strong>constraints</strong> on the set of legal relations.

If a relation $r$ fails to satisfy a given multivalued dependency, a relation $r^\prime$ that satisfies the multivalued dependency can be constructed by adding additional tuples to $r$.

<br>

## Closure: Armstrong’s axioms for MVD

The <strong>closure</strong> $D^+$ of $D$ is defined as the set of all functional and multivalued dependencies logically implied by $D$. Similar to closure of FDs, we can compute $D^+$ from $D$, using the formal definitions of functional dependencies and multivalued dependencies. We can manage with such reasoning for very simple multivalued dependencies, which seem to be most common in practice.

For complex dependencies, it is better to reason about sets of dependencies using a system of inference rules called <strong>Armstrong’s axioms</strong> for MVD:

<div style="border:1px solid blue; padding:10px;">

$\color{blue}{\mathbf{Definition.}}$ <span style="color:blue">Armstrong's Axioms for MVD</span><br>

Let $\alpha$, $\beta$, $\gamma$, and $\delta$ be sets of attributes of a relation schema $R$ with the set of multivalue dependency $D$.

<ul>
  <li><strong style="color:blue">Complementation rule</strong>: $\alpha \twoheadrightarrow \beta \implies \alpha \twoheadrightarrow R - \beta$ </li>
  <li><strong style="color:blue">Augmentation rule</strong>: $\alpha \twoheadrightarrow \beta \wedge \gamma \subseteq \delta \implies \alpha\delta \twoheadrightarrow \beta\gamma$</li>
  <li><strong style="color:blue">Transitivity rule</strong>: $\alpha \twoheadrightarrow \beta \wedge \beta \twoheadrightarrow \gamma \implies \alpha \twoheadrightarrow \gamma - \beta$</li>
  <li><strong style="color:blue">Replication rule</strong>: $\alpha \to \beta \implies \alpha \twoheadrightarrow \beta$</li>
  <li><strong style="color:blue">Coalescence rule</strong>: $\alpha \twoheadrightarrow \beta \wedge \exists \delta: (\delta \cap \beta = \varnothing \wedge \delta \to \gamma \wedge \gamma \subseteq \beta) \implies \alpha \to \gamma$</li>
</ul>

<details>
<summary style="color:blue">$\color{blue}{\mathbf{Proof.}}$ Soundness</summary>
<div markdown="1">

Since complementation rule and replication rule are trival, this proof only shows the soundness of remaining 3 rules.

<h4>Augmentation rule</h4>

Assume $\alpha \twoheadrightarrow \beta$ and $\gamma \subseteq \delta$. Since trivial FD $\delta \subseteq \gamma$ holds, by replication rule $\delta \twoheadrightarrow \gamma$ also holds. Then, 

* For any tuples $t_1, t_2 \in R$ with $t_1[\alpha] = t_2[\alpha]$ and $t_1[\delta] = t_2[\delta]$ (that is, $t_1[\alpha\delta] = t_2[\alpha\delta]$), there exist tuples $t_3, t_4 \in R$ such that
  * $t_3[\alpha\delta] = t_4 [\alpha\delta] = t_1[\alpha\delta]$
  * thus $t_3[\alpha] = t_4[\alpha] = t_1[\alpha]$ and $t_3[\delta] = t_4[\delta] = t_1[\delta]$
  * due to $\alpha \twoheadrightarrow \beta$
    * $t_3[\beta] = t_1[\beta]$
    * $t_3[R - \beta] = t_2[R - \beta]$
    * $t_4[\beta] = t_2[\beta]$
    * $t_4[R - \beta] = t_1[R - \beta]$
  * due to $\gamma \subseteq \delta$
    * $t_3[\delta] = t_1[\delta] \implies t_3[\gamma] = t_1[\gamma]$ 
    * $t_3[R - \beta] = t_2[R - \beta] \implies t_3[R - \beta\gamma] = t_2[R - \beta\gamma]$ since $R - \beta\gamma \subseteq R - \beta$
    * $t_4[\delta] = t_2[\delta] \implies t_4[\gamma] = t_4[\gamma]$ 
    * $t_4[R - \beta] = t_1[R - \beta] \implies t_4[R - \beta\gamma] = t_1[R - \beta\gamma]$ since $R - \beta\gamma \subseteq R - \beta$

This implies $\alpha\delta \twoheadrightarrow \beta\gamma$.

<h4>Transitivity rule</h4>

Assume $\alpha \twoheadrightarrow \beta$ and $\beta \twoheadrightarrow \gamma$. Then, 

* For any tuples $t_1, t_2 \in R$ with $t_1[\alpha] = t_2[\alpha]$, there exist tuples $t_3, t_4 \in R$ such that
  * $t_3 [\alpha] = t_4[\alpha] = t_1[\alpha] = t_2[\alpha]$
  * $t_3 [\beta] = t_1[\beta]$
  * $t_3 [R - \beta] = t_2[R - \beta] \implies t_3 [\gamma - \beta] = t_2[\gamma - \beta]$ since $\gamma - \beta \subseteq R - \beta$.
  * $t_4 [\beta] = t_2[\beta]$
  * $t_4 [R - \beta] = t_1[R - \beta] \implies t_4 [\gamma - \beta] = t_1[\gamma - \beta]$ since $\gamma - \beta \subseteq R - \beta$.
* From $\beta \twoheadrightarrow \gamma$, there exist $t_5, t_6 \in R$ such that
  * for $t_3[\beta] = t_1[\beta]$
    * $t_5 [\beta] = t_1[\beta]$
    * $t_5 [\gamma] = t_3[ \gamma ]$
    * $t_5 [R - \gamma] = t_1 [R - \gamma]$
    * that is, $t_5 [R - (\gamma - \beta)] = t_1 [R - (\gamma - \beta)]$ since $R - (\gamma - \beta) = (R - \gamma) \cup \beta$
  * for $t_4[\beta] = t_2[\beta]$
    * $t_6 [\beta] = t_2[\beta]$
    * $t_6 [\gamma] = t_4[ \gamma ]$
    * $t_6 [R - \gamma] = t_2 [R - \gamma]$
    * that is, $t_6 [R - (\gamma - \beta)] = t_2 [R - (\gamma - \beta)]$ since $R - (\gamma - \beta) = (R - \gamma) \cup \beta$

Hence, there exist tuples $t_5, t_6 \in R$ such that
  * $t_5[\alpha] = t_1[\alpha] = t_2[\alpha]$ since for any attribute $A \in \alpha$
    * If an attribute $A \in (R - \gamma) \cap \alpha$
      * $t_5 [A] = t_1[A]$ by $t_5 [R - \gamma] = t_1 [R - \gamma]$
    * If an attribute $A \in \gamma \cap \alpha$
      * $t_5 [A] = t_3[A]$ by $t_5 [\gamma] = t_3[ \gamma ]$
      * $t_3 [A] = t_1[A]$ by $t_3[\alpha] = t_1[\alpha]$
  * $t_6[\alpha] = t_1[\alpha] = t_2[\alpha]$ analogously
  * $t_5[ \gamma - \beta ] = t_3 [ \gamma - \beta ] = t_2[\gamma - \beta]$
  * $t_5[ R - (\gamma - \beta)] = t_1 [ R - (\gamma - \beta)]$
  * $t_6[ \gamma - \beta ] = t_4 [ \gamma - \beta ] = t_1[\gamma - \beta]$
  * $t_6[ R - (\gamma - \beta)] = t_2 [ R - (\gamma - \beta)]$


<h4>Coalesence rule</h4>

Assume $\alpha \twoheadrightarrow \beta$ and there exists $\delta \subset R$ such that $\delta \cap \beta = \varnothing$, $\delta \to \gamma$, $\gamma \subseteq \beta$. Then, 

* For any tuples $t_1, t_2 \in R$ with $t_1[\alpha] = t_2[\alpha]$, 
  * By $\alpha \twoheadrightarrow \beta$, there exist tuples $t_3 \in R$ such that
    * $t_1 [\alpha] = t_2[\alpha] = t_3 [\alpha]$
    * $t_3[\beta] = t_1[\beta] \implies t_3[\gamma] = t_1[\gamma]$ since $\gamma \subseteq \beta$
    * $t_3[R - \beta] = t_2[R - \beta] \implies t_3[\delta] = t_2[\delta]$ since $\delta \subseteq R - \beta$
  * From $\delta \to \gamma$, $t_3[\delta] = t_2[\delta]$ implies $t_3 [\gamma] = t_2 [\gamma]$. 
  * That is, $t_1[ \gamma] = t_2[\gamma]$

This implies $\alpha \to \gamma$.

$$\tag*{$\blacksquare$}$$

</div>
</details>

</div><br>

We can further simplify calculating the closure of $D$ by using the following rules, derivable from the previous ones: 

<div style="border:1px solid #bf1100; padding:10px;">

$\color{#bf1100}{\mathbf{Remark.}}$ <span style="color:#bf1100">Addtional rules</span><br>

Some additional rules can be inferred from Armstrong’s axioms:

<ul>
  <li><strong>Union rule</strong>: $\alpha \twoheadrightarrow \beta \wedge \alpha \twoheadrightarrow \gamma \implies \alpha \twoheadrightarrow \beta \gamma$ </li>
  <li><strong>Intersection rule</strong>: $\alpha \twoheadrightarrow \beta \wedge \alpha \twoheadrightarrow \gamma \implies \alpha \twoheadrightarrow \beta \cap \gamma$ </li>
  <li><strong>Difference rule</strong>: $\alpha \twoheadrightarrow \beta \wedge \alpha \twoheadrightarrow \gamma \implies \alpha \twoheadrightarrow \beta - \gamma \wedge \alpha \twoheadrightarrow \gamma - \beta$</li>
</ul>

<details>
<summary style="color:#bf1100">$\color{#bf1100}{\mathbf{Proof.}}$ Soundness</summary>
<div markdown="1">

<h4>Union rule</h4>

$$
\begin{aligned} 
  & \alpha \twoheadrightarrow \gamma & \\
  & \alpha \twoheadrightarrow \alpha\gamma & \textrm{ augmentation rule } \\
  & \alpha \twoheadrightarrow \beta & \\
  & \alpha\gamma \twoheadrightarrow \beta\gamma & \textrm{ augmentation rule } \\
  & \alpha\gamma \twoheadrightarrow R - \alpha\beta\gamma & \textrm{ complementation rule } \\
  & \alpha \twoheadrightarrow R - \alpha\beta\gamma & \textrm{ transitivity rule } \\
  & \alpha \twoheadrightarrow \beta\gamma & \textrm{ complementation rule } \\
\end{aligned}
$$

<h4>Intersection rule</h4>

$$
\begin{aligned} 
  & \alpha \twoheadrightarrow R - \gamma & \textrm{ complementation rule } \\
  & \alpha \twoheadrightarrow R - \beta & \textrm{ complementation rule } \\
  & \alpha \twoheadrightarrow (R - \beta\gamma) \cup (\beta \cap \gamma) & \textrm{ union rule } \\
  & \alpha \twoheadrightarrow \beta \cap \gamma & \textrm{ complementation rule } \\
\end{aligned}
$$

<h4>Difference rule</h4>

This rule can be simply derived from complementation rule and intersection rule. By complementation rule, $\alpha \twoheadrightarrow R - \gamma = \gamma^c$. By intersection rule between $\alpha \twoheadrightarrow \gamma^c$ and $\alpha \twoheadrightarrow \beta$, $\alpha \twoheadrightarrow \beta \cap \gamma^c = \beta - \gamma$ holds.

$$\tag*{$\blacksquare$}$$

</div>
</details>

</div><br>

## Lossless Decomposition

Let $r(R)$ be a relation schema, and let $D$ be a set of functional and multivalued dependencies on $R$. Let $r_1(R_1)$ and $r_2(R_2)$ form a decomposition of $R$. This decomposition of $R$ is <strong>lossless</strong> if and only if at least one of the following multivalued dependencies is in $D^+$:

$$
\begin{gathered}
  R_1 \cap R_2 \twoheadrightarrow R_1 \\
  R_1 \cap R_2 \twoheadrightarrow R_2
\end{gathered}
$$

<br>

# Reference
**[1]** [Silberschatz, Abraham, Henry F. Korth, and Shashank Sudarshan. "Database system concepts." (2011).](https://db-book.com/) <br>
**[2]** [C.Beeri, R.Fagin, and J.H.Howard, "A Complete Axiomatization for Functional and Multivalued Dependencies in Database Relations", Proc. of 1977 ACM SIGMOD Conference](https://dl.acm.org/doi/10.1145/509404.509414) <br>

