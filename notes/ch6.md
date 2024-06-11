---
title:  "[DB] E-R Model" 
excerpt: ""
categories:
  - database-system
tags:
  - [Computer Science, DB]
header:
  teaser: "/assets/images/database-system/teaser/er-model.png"

toc: true
toc_sticky: true

date: 2024-04-01
last_modified_at: 2024-04-01

lang: en
ref: ERModel
use_math: true
---

The <strong>entity-relationship (E-R)</strong> data model models an enterprise as a collection of entities and relationships that represents the overall logical structure of a database.

* <strong>Entity</strong>: a "thing" or "object" in the enterprise that is distinguishable from other objects, which is described by a set of <strong>attributes</strong> 
    * e.g. <code>person</code> with its <code>person_id</code> in a university
* <strong>Relationship</strong>: an association among several entities
    * e.g. <code>advisor</code> relation that associates instructor Katz with student Shankar

The E-R model also has an associated diagrammatic representation called the <strong>E-R diagram</strong>, which can express the overall logical structure of a database graphically.

<strong>※ Note</strong> <br>
There is no universal standard for E-R diagram notation, and different books and ER diagram software use different notations. In this post, we follow the notation in the reference textbook, which is close to the UML diagram; we do not use another popular notation, called Chen's notation. 

<br>

# Basic Components

The E-R data model employs <em>three</em> basic components:

* <strong>Entity set</strong>
* <strong>Relationship set</strong>
* <strong>Attribute (E-R)</strong>

## Entity Set

* An <strong>entity</strong> is an object that exists and is distinguishable from other objects.
    * e.g., specific person, company
* An <strong>entity set</strong> is a set of entities of the same type that share the same properties. 
    * e.g., set of all instructors, students
* An entity is represented by a set of <strong>attributes</strong>, and each entity has a <strong>value</strong> for each of its attributes.
    * e.g., <code>instructor = (ID, name, salary)</code>
* Entity sets can be represented graphically as follows:
    * rectangles represent entity sets
    * attributes are listed inside an entity rectangle
    * underline indicates the primary key attributes

<p align = "center">
<img width="400" alt="image" src="/assets/images/database-system/er-model/entity-set-example-in-e-r.png">
<figcaption style="text-align: center;">$\mathbf{Fig\ 1.}$ E-R diagram showing entity sets <code>instructor</code> and <code>student</code> (<a href="#reference">Silberschatz et al.</a>) </figcaption>
</p>

<p align = "center">
<img width="400" alt="image" src="/assets/images/database-system/er-model/entity-set-example.png">
<figcaption style="text-align: center;">$\mathbf{Fig\ 2.}$ Examples of Entity Set Instances (<a href="#reference">Silberschatz et al.</a>) </figcaption>
</p>

## Relationship set

* A <strong>relationship</strong> is an association among several entities. 
    * e.g. <em>advisor</em> that associates <em>instructor</em> Katz with <em>student</em> Shankar
* A <strong>relationship instance</strong> in an E-R schema represents an association between the named entities in the real-world enterprise that is being modeled.
    * e.g. the individual <em>instructor</em> entity Katz, who has instructor ID 45565, and the <em>student</em> entity Shankar, who has student ID 12345

<p align = "center">
<img width="350" alt="image" src="/assets/images/database-system/er-model/relationship-example.png">
<figcaption style="text-align: center;">$\mathbf{Fig\ 3.}$ Examples of Relationship Instances (<a href="#reference">Silberschatz et al.</a>) </figcaption>
</p>

* A <strong>relationship set</strong> is a set of relationships of the same type. It is a <strong>mathematical relation</strong> on $n \geq 2$ entity sets that are not necessarily distinct.
    * If $E_1​, \cdots, E_n$​ are entity sets and $(e_1, \cdots, e_n)$ is a relationship instance, then a relationship set $R$ is a subset of

    $$
    \{ (e_1, \cdots, e_n) \vert e_j \in E_j \text{ where } j = 1, \cdots, n \}
    $$
    
    * e.g. <code>(student_id = 12, instructor_id = 34) ∈ advisor</code>
    * The entity sets $E_1​, \cdots, E_n$​ are said to <strong>participate</strong> in the relationship set $R$.
* In ER diagram, diamonds represent relationship sets:

<p align = "center">
<img width="500" alt="image" src="/assets/images/database-system/er-model/relationship-example-in-er.png">
<figcaption style="text-align: center;">$\mathbf{Fig\ 4.}$ E-R diagram showing relationship set <em>advisor</em> (<a href="#reference">Silberschatz et al.</a>) </figcaption>
</p><br>

* The function that an entity plays in a relationship is called that entity’s <strong>role</strong>. Each occurrence of an entity set plays a role in the relationship. 
    * e.g. in <code>course</code> entities, one course is a prerequisite for another course, characterized by <code>(course_id, prereq_id)</code>
    * Since entity sets participating in a relationship set are generally distinct, roles are implicit and are not usually specified.
    * But in some scenarios, e.g. the entity sets of a relationship set are not distinct, they are useful when the meaning of a relationship needs clarification.

    <p align = "center">
    <img width="500" alt="image" src="/assets/images/database-system/er-model/relationship-role.png">
    <figcaption style="text-align: center;">$\mathbf{Fig\ 4.}$ E-R diagram showing relationship set with role indicator (<a href="#reference">Silberschatz et al.</a>) </figcaption>
    </p><br>


* An relationship may also have attributes called <strong>descriptive attirbutes</strong> that are associated with a relationship set.
    * e.g. the <em>advisor</em> relationship set between entity sets <em>instructor</em> and <em>student</em> may have the attribute <em>date</em> which tracks when the <em>student</em> started being associated with the <em>advisor</em>
    * In ER diagram, represented with a dashed line to the diamond of relationship set

<p align = "center">
<img width="600" alt="image" src="/assets/images/database-system/er-model/relationship-with-attribute.png">
<figcaption style="text-align: center;">$\mathbf{Fig\ 5.}$ E-R diagram with an attribute attached to a relationship set. (<a href="#reference">Silberschatz et al.</a>) </figcaption>
</p><br>

<p align = "center">
<img width="500" alt="image" src="/assets/images/database-system/er-model/relationship-example-2.png">
<figcaption style="text-align: center;">$\mathbf{Fig\ 6.}$ E-R diagram showing relationship set <em>advisor</em> (<a href="#reference">Silberschatz et al.</a>) </figcaption>
</p><br>

<h4>Nonbinary relationship sets</h4>

* The number of entity sets that participate in a relationship set is the <strong>degree</strong> of the relationship set.
    * A binary set relationship set is of degree 2.
        * e.g. <em>takes</em> relationship between <em>student</em> and <em>section</em>.
    * A ternary set relationship set is of degree 3.
        * e.g. <em>proj_guide</em> relationship that represents a particular student is guided by a particular in- structor on a particular project, between <em>instructor</em>, <em>student</em>, and <em>project</em>

## Attributes

* An <strong>attribute</strong> of an entity set is a function that maps from the entity set into a <strong>domain</strong>. 
    * A set of permitted values for each attribute is called <strong>domain</strong> or <strong>value set</strong>.
    * Since an entity set may have several attributes, each entity can be described by a set of <code>(attribute, data_value)</code> pairs, one pair for each attribute of the entity set.
* An attribute, as used in the E-R model, can be characterised by the following attribute types.
    * <strong>Simple / Composite</strong>
        * <strong>Simple</strong> attributes are not divided into subparts
            * e.g. <code>street</code>, <code>city</code>, <code>state</code>, and <code>postal_code</code>
        * <strong>Composite</strong> attributes can be divided into subparts.
            * e.g. <code>address</code>

        <p align = "center">
        <img width="600" alt="image" src="/assets/images/database-system/er-model/composite-attribute.png">
        <figcaption style="text-align: center;">$\mathbf{Fig\ 7.}$ Composite Attributes (<a href="#reference">Silberschatz et al.</a>) </figcaption>
        </p>

    * <strong>Single-valued / Multivalued</strong>
        * <strong>Single-valued</strong> attributes have a single value for a particular entity.
            * e.g. <code>instructor_id</code>
        * <strong>Multi-valued</strong> attributes have a set of values for a specific entity.
            * e.g. <code>phone_numbers</code> (an instructor may have two phone numbers)

    * <strong>Derived</strong>
        * The value of derived attributes can be derived from the values of other related attributes or entities.
            * e.g. <code>age</code>: we can calculate age from <code>date_of _birth</code> and the current date.

<br><br>

<p align = "center">
<img width="200" alt="image" src="/assets/images/database-system/er-model/complex-attributes.png">
<figcaption style="text-align: center;">$\mathbf{Fig\ 8.}$ E-R diagram with composite, multivalued, and derived attributes. (<a href="#reference">Silberschatz et al.</a>) </figcaption>
</p><br>

* An attribute takes a <strong>null</strong> value when an entity does not have a value for it.

<br>

# Constraints

An E-R enterprise schema may define certain constraints to which the contents of a database must conform.

## Mapping Cardinality Constraints

* <strong>Mapping cardinality</strong> expresses the number of entities to which another entity can be associated via a relationship set. It is most useful in describing binary relationship sets.

* For a binary relationship set $R$ between entity sets $A$ and $B$, the mapping cardinality must be one of the following:

    * <strong>One-to-one</strong>
        * An entity in $A$ is associated with at most one entity in $B$, and vice versa.
    * <strong>One-to-many</strong>
        * An entity in $A$ is associated with any number (zero or more) of entities in $B$. An entity in $B$ can be associated with at most one entity in $A$.
    * <strong>Many-to-one</strong>
        * An entity in $A$ can be associated with at most one entity in $B$. An entity in $B$ is associated with any number (zero or more) of entities in $A$.
    * <strong>Many-to-many</strong>
        * An entity in $A$ is associated with any number (zero or more) of entities in $B$, and vice versa.

<p align = "center">
<img width="500" alt="image" src="/assets/images/database-system/er-model/mapping-cardinality.png">
<figcaption style="text-align: center;">$\mathbf{Fig\ 9.}$ Mapping Cardinalities (<a href="#reference">Silberschatz et al.</a>) </figcaption>
</p><br>

* In ER diagram, between the relationship set and the entity set, we express cardinality constraints by drawing either
    * directed line ($\rightarrow$), signifying "one" or 
    * undirected line (⎯⎯⎯⎯), signifying "many" 

<p align = "center">
<img width="700" alt="image" src="/assets/images/database-system/er-model/mapping-cardinality-er.png">
<figcaption style="text-align: center;">$\mathbf{Fig\ 10.}$ Mapping Cardinalities in ER diagrams (<a href="#reference">Silberschatz et al.</a>) </figcaption>
</p><br>

## Participation Constraints

Let $E$ be an entity set and $R$ be a relationship set.

* <strong>Total participation</strong>
    * The participation of $E$ in $R$ is said to be <strong>total</strong> if every entity in $E$ participates in at least one relationship of $R$.
    * e.g. every <em>student</em> must have at least one <em>advisor</em>
* <strong>Partial participation</strong>
    * The participation of $E$ in $R$ is said to be <strong>partial</strong> if only some entities in $E$ participate in relationships in $R$.

* In ER diagram, total participation of an entity in a relationship set is represented using double lines.

<p align = "center">
<img width="600" alt="image" src="/assets/images/database-system/er-model/participation-er.png">
<figcaption style="text-align: center;">$\mathbf{Fig\ 11.}$ E-R diagram showing total participation (<a href="#reference">Silberschatz et al.</a>) </figcaption>
</p><br>

## Maximum/Minimum Cardinality Constraints

* A line in ER diagram may have an associated minimum and maximum cardinality, shown in the form $\ell .. h$, where $\ell$ is the <strong>minimum</strong> and $h$ the <strong>maximum</strong> cardinality
    * A minimum value of $1$ indicates total participation
    * A maximum value of $1$ indicates that the entity participates in at most one relationship
    * A maximum value of $*$ indicates no limit

<p align = "center">
<img width="600" alt="image" src="/assets/images/database-system/er-model/min-max-cardinality-er.png">
<figcaption style="text-align: center;">$\mathbf{Fig\ 11.}$ E-R diagram showing max/min cardinalities, equivalent to one-to-many cardinality (<a href="#reference">Silberschatz et al.</a>) </figcaption>
</p><br>

<br>

# Primary Key

<strong>Primary keys</strong> provide a way to specify how entities and relationships are distinguished. We will consider the primary keys for the followings:

* <strong>Weak entity sets</strong>
* <strong>Entity sets</strong>
* <strong>Relationship sets</strong>

## Entity Sets

* By definition, individual entities are <em>distinct</em>. However, from a database perspective, the differences among them must be expressed in terms of their attributes.
* Therefore, the values of the attribute values of an entity must be such that they can <em>uniquely identify</em> the entity.
* The concepts of superkey, candidate key, and primary key are applicable to entity sets just as they are applicable to relation schemas.

## Relationships

* To distinguish among the various relationships of a relationship set, the individual primary keys of the entities in the relationship set are used.
    * Let $R$ be a relationship set involving entity sets $E_1, \cdots, E_n$​.
    * Let $$A= \left\{ a_1, \cdots, a_m \right\}$$ be the set of attributes assoiciated with $R$, which may be $\varnothing$. 
    * Then, the primary key for $R$ consists of the union of the primary keys of the entity sets $E_1, \cdots, E_n$, and also is own attributes $A$. 
    * That is, the following set of attributes describes an individual relationship in set of for $R$:

        $$
        \left(\bigcup_{i=1}^n \text { primary-key }\left(E_i\right)\right) \cup A
        $$

    * A relationship set is a set of relationship instances, and each instance is uniquely identified by the entities that participate in it. Thus the following set of attributes forms a super key for $R$:

        $$
        \bigcup_{i=1}^n \text { primary-key }\left(E_i\right)
        $$

* The choice of the primary key for a relationship set depends on the mapping cardinality of the relationship set.

    * <strong>Many-to-many</strong>: the above union of the primary keys is a minimal superkey and is chosen as the primary key. 
    * <strong>One-to-many, Many-to-one</strong>: the primary key of the <strong><em>"many"</em></strong> side is a minimal superkey and is used as the primary key
    * <strong>One-to-one</strong>: the primary key of either one of the participating entity sets forms a minimal superkey, and either one can be chosen as the primary key

## Weak Entity Sets

<h4>Motivation</h4>

* In some scenarios, the primary key of an entity in a relationship is redundant when describing the relationship. 
    * e.g. a relationship set <code>sec_course</code> between <code>section</code>, that already has an attribute <code>course_id</code>, and <code>course</code>
* In this case, an alternative way to deal with this redundancy is to not store the redundant attribute in the entity.
    * e.g. in <code>sec_course</code> only store the remaining <code>sec_id</code>, <code>year</code>, and <code>semester</code>.
    * However, these are not enough to identify a particular <code>section</code> entity uniquely.

<br>

<h4>Description</h4>

* A <strong>weak entity</strong> is one whose existence is dependent on another entity, called its <strong>identifying entity</strong>. 
    * e.g. <code>section</code> of the course can be opened only if <code>course</code> exists
    * The identifying entity set is said to <strong>own</strong> the weak entity set.
    * It is <strong>existence dependent</strong>; every weak entity must be associated with an identifying entity.
* An entity set that is not a weak entity set is termed a <strong>strong entity</strong> set.
* Instead of associating a primary key with a weak entity, we use the identifying entity, along with extra attributes called a <strong>discriminator</strong> or <strong>partial key</strong> to uniquely identify a weak entity.
* The relationship associating the weak entity set with the identifying entity set is called the <strong>identifying relationship</strong>.
    * It is <em>many-to-one</em> from the weak entity set to the identifying entity set, and the participation of the weak entity set in the relationship is always total.
* A weak entity set can participate in relationships other than the identifying relationship. 
    * e.g. <code>section</code> entity could participate in a relationship with the <code>time_slot</code> entity set, identifying the time when a particular class section meets.
    * In this case, a weak entity set may participate as owner in an identifying relationship with another weak entity set. 
* It is also possible to have a weak entity set with more than one identifying entity set. 
* The <strong>primary key</strong> of the weak entity set would consist of the union of the primary keys of the identifying entity sets, plus the discriminator of the weak entity set.

<br>

* In ER diagrams, a weak entity set is depicted via a <em>double rectangle</em>
    * We underline the discriminator of a weak entity set with a <em>dashed line</em>
    * The relationship set connecting the weak entity set to the identifying strong entity set is depicted by a <em>double diamond</em>

<p align = "center">
<img width="600" alt="image" src="/assets/images/database-system/er-model/weak-entity-er.png">
<figcaption style="text-align: center;">$\mathbf{Fig\ 12.}$ E-R diagram with a weak entity set. (<a href="#reference">Silberschatz et al.</a>) </figcaption>
</p><br>

<h4>Examples</h4>

* The relationship <code>sec_course</code> is the identifying relationship
    * the identifying entity set for <code>section</code> is <code>course</code>
    * the primary key of <code>section</code> is formed by the primary key of the identifying entity set <code>course</code> plus the discriminator of the weak entity set.
    * thus the primary key is <code>course_id</code>, <code>sec_id</code>, <code>year</code>, and <code>semester</code>.
* The existence of <code>rooms</code> is entirely dependent on the existence of a <code>hotel</code>
    * Thus, <code>room</code> can be seen as the weak entity of the <code>hotel</code>
* The <code>bank_account</code> of a particular <code>bank</code> has no existence if the <code>bank</code> doesn’t exist anymore 
* A <code>company</code> may store the information of <code>dependents</code> (parents, children, spouse) of an <code>employee</code>, but the <code>dependents</code> don’t have existence without the employee.
    * A <code>dependent</code> will be a weak entity, and an employee will be the identifying entity for <code>dependent</code>

<br>

# Redundant Attributes

* Once the entities and their corresponding attributes are chosen, the relationship sets among the various entities are formed.
* Suppose we have two entity sets:
    * <code>student</code>, with attributes <code>ID</code>, <code>name</code>, <code>tot_cred</code>, <code>dept_name</code>
    * <code>department</code>, with attributes <code>dept_name</code>,<code>building</code>, <code>budget</code>
* We model the fact that each student has an associated <code>department</code> using a relationship set <code>stud_dept</code>
* The attribute <code>dept_name</code> in student replicates information present in the relationship and is therefore <strong>redundant</strong> and needs to be removed
    * But, when we create a relational schema from the E-R diagram, the attribute <code>dept_name</code> gets added to the relation <code>instructor</code>, but only if each instructor has at most one associated department.
    * If an instructor has more than one associated department, the relationship between instructors and departments is recorded in a separate relation <code>inst_dept</code>.


<p align = "center">
<img width="600" alt="image" src="/assets/images/database-system/er-model/university-er.png">
<figcaption style="text-align: center;">$\mathbf{Fig\ 13.}$ E-R diagram with a university enterprise. (<a href="#reference">Silberschatz et al.</a>) </figcaption>
</p><br>

# Reduction to Relational Schema

Entity sets and relationship sets can be expressed uniformly as relation schemas that represent the contents of the database. A database which conforms to an ER diagram can be represented by a collection of schemas. For each entity set and relationship set, there is a unique schema that is assigned the name of the corresponding entity set or relationship set. Each schema has a number of columns (generally corresponding to attributes), which have unique names.

## Representation of Entity Sets

* A <strong>strong entity set</strong> reduces to a schema with the same attributes
    * e.g., <code>student (<strong>ID</strong>, name, tot_cred)</code>
* A <strong>weak entity set</strong> becomes a table that includes a column for the primary key of the identifying strong entity set            
    * e.g., <code>section (<strong>course_id</strong>, <strong>sec_id</strong>, <strong>semester</strong>, <strong>year</strong>)</code>
* <strong>Composite attributes</strong> are flattened out by creating a separate attribute for each component attribute.
    * e.g., with composite attribute <code>name</code> with component attributes <code>first_name</code>, and <code>last_name</code>
* A <strong>multi-valued attribute</strong> $M$ of an entity $E$ is represented by a separate schema $EM$.
    * The schema $EM$ has attributes corresponding to the primary key of $E$, and an attribute corresponding to the multi-valued attribute $M$.
        * e.g., Multi-valued attribute <code>phone_number</code> of <code>instructor</code> is represented by <code>inst-phone(ID​, phone_number​)</code>
   * And each value of the multi-valued attribute maps to a separate tuple of the relation on the schema $EM$.

## Representation of Relationship Sets

* A <strong>many-to-many</strong> relationship is represented as a schema with attributes for the primary keys of the two participating entity sets, and any descriptive attributes of the relationship set.
    * e.g., Schema for a relationship set <code>advisor</code> between <code>student</code> and <code>instructor</code> is <code>advisor(student_id​, instructor_id​)</code>;
* <strong>Many-to-one</strong> and <strong>one-to-many</strong> relationship sets that are <strong>total</strong> on the many-side can be represented by adding an extra attribute to the 'many' side, containing the primary keys of the 'one' side.
    * e.g., Instead of creating a new schema for a relationship set <code>inst-dept</code> between <code>instructor</code> and <code>department</code> with many-to-one mapping, add an attribute <code>dept_name</code> to the schema arising from the entity set <code>instructor</code>.
    * e.g. <code>stud_dept</code> $\Rightarrow$ <code>student(<strong>ID</strong>, name, dept_name, credits)</code>
    * If the participation if partial on the 'many' side, this could result in <code>NULL</code> values.
* A <strong>one-to-one</strong> relationship sets, either side can be chosen to act as the 'many' side.
    * An extra attribute can be added to either of the tables corresponding to the two entity sets
    
## Redundancy of Schemas

* The schema corresponding to a relationship set linking a weak entity set to its identifying strong entity set is redundant.
    * e.g., The <code>section</code> schema already contains the attributes that would appear in the <code>sec-course</code> schema, thus <code>sec-course</code> is redundant.
* Hence it does not need to be present in a relational database design based upon an E-R diagram.
<br>


# Extended E-R features

Although the basic E-R concepts can model most database features, some aspects of a database may be more aptly expressed by certain extensions to the basic E-R model. 

## Specialization

In <strong>top-down design process</strong>, an entity set may include sub-grouping of entities that are distinct in some way from other entities in the set. The process of designating subgroups within an entity set is called <strong>specialization</strong>.
* e.g., the specialization of <code>person</code> allows us to distinguish among person entities according to whether they correspond to <code>employee</code> or <code>student</code>.

These subgroupings become lower-level entity sets that have attributes or participate in relationships that do not apply to the higher-level entity set. Hence, the specialization relationship may also be referred to as a <strong>superclass-subclass</strong> relationship.

### Representation of Specializations

In terms of an E-R diagram, it is depicted by a <em>hollow arrow-head</em> pointing from the specialized entity to the other entity. We refer to this relationship as the <strong>IS A</strong> relationship, which stands for "is a" and represents, for example, that <code>instructor</code> "is a" <code>employee</code>.

<p align = "center">
<img width="500" alt="Specialization and Generalization" src="/assets/images/database-system/er-model/specialization-and-generalization.png">
<figcaption style="text-align: center;">$\mathbf{Fig\ 14.}$ Specialization & Generalization (<a href="#reference">Silberschatz et al.</a>) </figcaption>
</p><br>

The way we depict specialization in an E-R diagram depends on whether an entity may belong to multiple specialized entity sets (<strong>overlapping specialization</strong>) or if it must belong to at most one specialized entity set (<strong>disjoint specialization</strong>). 

<h4>Method 1</h4>

1. Form a schema for the higher level entity.
2. Form a schema for each of the lower level entity set, and include the primary key of the higher level entity set and local attributes.

e.g.,

<table style="width:90%;border-collapse:collapse;border-spacing:0;margin-left:auto;margin-right:auto; auto" class="center"><thead><tr><th style="border-color:inherit;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;font-weight:normal;overflow:hidden;padding:10px 5px;text-align:center;vertical-align:top;word-break:normal"><strong>Schema</strong></th><th style="border-color:inherit;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;font-weight:normal;overflow:hidden;padding:10px 5px;text-align:center;vertical-align:top;word-break:normal"><strong>Attributes</strong></th></tr></thead><tbody><tr><td style="border-color:inherit;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">person</td><td style="border-color:inherit;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">ID, name, street, city</td></tr><tr><td style="border-color:inherit;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">student</td><td style="border-color:inherit;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">ID, credits</td></tr><tr><td style="border-color:inherit;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">employee</td><td style="border-color:inherit;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">ID, salary</td></tr></tbody></table>

* <strong>Drawbacks</strong>: Accessing information about an lower level entity requires accessing two relations (the one corresponding to the lower level schema, and the one corresponding to the higher level schema).

<h4>Method 2</h4>

1. Form a schema for each entity set with all local and inherited attributes.

e.g.,

<table style="width:90%;border-collapse:collapse;border-spacing:0;margin-left:auto;margin-right:auto; auto" class="center"><thead><tr><th style="border-color:inherit;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;font-weight:normal;overflow:hidden;padding:10px 5px;text-align:center;vertical-align:top;word-break:normal"><strong>Schema</strong></th><th style="border-color:inherit;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;font-weight:normal;overflow:hidden;padding:10px 5px;text-align:center;vertical-align:top;word-break:normal"><strong>Attributes</strong></th></tr></thead><tbody><tr><td style="border-color:inherit;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">person</td><td style="border-color:inherit;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">ID, name, street, city</td></tr><tr><td style="border-color:inherit;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">student</td><td style="border-color:inherit;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">ID, name, street, city, credits</td></tr><tr><td style="border-color:inherit;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">employee</td><td style="border-color:inherit;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;text-align:left;vertical-align:top;word-break:normal">ID, name, street, city, salary</td></tr></tbody></table>

* <strong>Drawbacks</strong>: Some attributes may be stored redundantly for entities that belongs to two or more specialized entity sets.
    * e.g. <code>name</code>, <code>street</code>, and <code>city</code> may be stored redundantly for people who are both <code>students</code> and <code>employees</code>

## Generalization

Conversely, <strong>generalization</strong> is a containment relationship that exists between a higher-level entity set and one or more lower-level entity sets. This is a <strong>bottom-up design</strong> process, in which multiple entity sets are synthesised into a higher-level entity set on the basis of common features.

Specialization and generalization are simple inversions of each other and represented in an ER diagram in the same way; thus these terms are used interchangeably.

## Constraints on Specializations/Generalizations

To model an enterprise more accurately, the database designer may choose to place certain constraints on a particular generalization and specialization.

### Disjoint/Overlapping Constraint

One type of constraint on specialization specifies whether a specialization is disjoint or overlapping.

* <strong>Disjoint specialization</strong>
    * A disjoint constraint requires that an entity belong to at most one lower-level entity set.
    * e.g. <code>instructor</code> and <code>secretary</code>
* <strong>Overlapping specialization</strong>
    * An overlapping constraint requires that an entity may belong to multiple lower-level entity sets within a single specialization.
    * e.g. <code>employee</code> and <code>student</code>

### Completeness Constraint

The <strong>completeness constraint</strong> specifies whether or not an entity in the higher-level entity set must belong to at least one of the lower-level entity sets within the generalization.

* <strong>Total specialization/generalization</strong>
    * Each higher-level entity must belong to one of the lower-level entity sets;
    * We can specify it in an ER diagram by adding the keyword "total" in the diagram and drawing a dashed line from the keyword to the corresponding hollow arrow-head to which it applies or to the set of hollow arrow-heads to which it applies.

* <strong>Partial specialization/generalization</strong>
    * Some higher-level entities may not belong to any lower-level entity sets;


<p align = "center">
<img width="400" alt="E-R diagram representation of completeness constraint" src="/assets/images/database-system/er-model/completeness-constraint.png">
<figcaption style="text-align: center;">$\mathbf{Fig\ 15.}$ E-R diagram representation of completeness constraint: the student generalization is total: <strong>ALL</strong> student entities must be either graduate or undergraduate (<a href="#reference">Silberschatz et al.</a>) </figcaption>
</p><br>

## Aggregation

* <strong>Limitation</strong>: E-R model cannot express relationships among relationships.
* <strong>Example</strong>
    * consider the ternary relationship <code>proj_guide</code>, which we saw earlier.
    * suppose we want to record evaluations of a student by a guide on project, in <code>eval_for</code>.
    * Then there is redundancy between <code>eval_for</code> and <code>proj_guide</code> overlapped;
        * every <code>instructor</code>, <code>student</code>, <code>project</code> combination in <code>eval_for</code> must also be in <code>proj_guide</code>.

<strong>Aggregation</strong> is an abstraction through which relationships are treated as higher-level entities. By treating relationship as an abstract entity, relationships between relationships can be defined, thereby reducing unnecessary redundancy.

* e.g., regard <code>proj_guide</code> as a higher-level entity set, then create a binary relationship <code>eval_for</code> between <code>proj_guide</code> and <code>evaluation</code>;

<p align = "center">
<img width="500" alt="E-R diagram representation with aggregation." src="/assets/images/database-system/er-model/aggregation.png">
<figcaption style="text-align: center;">$\mathbf{Fig\ 16.}$ E-R diagram representation with aggregation. (<a href="#reference">Silberschatz et al.</a>) </figcaption>
</p><br>


### Representation of Aggregations

To represent aggregation, create a schema containing
* The primary key of the aggregated relationship
* The primary key of the associated entity set
* Any descriptive attributes, if exists

In our example, the schema <code>eval_for</code> is <code>eval_for (s_ID, project_id, i_ID, evaluation_id)</code>.

Note that the schema <code>proj_guide</code> is redundant (thus can be removed), provided that we are allowed to store <code>NULL</code> values for the <code>score</code> attribute in <code>evaluation</code>.
* <code>evaluation (id, score)</code>: <code>score</code> could be <code>NULL</code> if the corresponding <code>(instructor, student, project)</code> combination was not evaluated;

<br>

# E-R Design Issues

The notions of an entity set and a relationship set are not precise, and it is possible to define a set of entities and the relationships among them in a number of different ways.

## Common mistakes in E-R models

* A common mistake when creating E-R models:
    * Use of a primary key of an entity set as an attribute of another entity set, instead of using a relationship;
    * Use of a relationship with a single-valued attribute in a situation that requires a multi-valued attribute;
        * e.g., we can only represent a single assignment for a given student-section pair if we add <code>assignment</code> and <code>marks</code> to the relationship <code>take</code>
<br>

<p align = "center">
<img width="500" alt="Example of erroneous E-R diagrams" src="/assets/images/database-system/er-model/erroneous.png">
<figcaption style="text-align: center;">$\mathbf{Fig\ 17.}$ Example of erroneous E-R diagrams. (<a href="#reference">Silberschatz et al.</a>) </figcaption>
</p><br>

* Possible solutions:
    * Use a weak entity set;
        * e.g. model <code>assignment</code> as a weak entity identified by <code>section</code>, and to add a relationship <code>marks</code> in between <code>assignment</code> and <code>student</code>
    * Use a multivalued composite attribute;
        * e.g. use <code>{assignment_marks}</code> to <code>takes</code>, where it has component attributes <code>assignment</code> and <code>marks</code>

<p align = "center">
<img width="700" alt="Correct versions of the E-R diagram" src="/assets/images/database-system/er-model/correct.png">
<figcaption style="text-align: center;">$\mathbf{Fig\ 18.}$ Correct versions of the E-R diagram. (<a href="#reference">Silberschatz et al.</a>) </figcaption>
</p><br>

## Entity Sets vs. Attributes

Consider the entity set <code>instructor</code> with the additional attribute <code>phone_number</code>. There are two possible ways to model this as an E-R model:
* <code>phone_number</codE> is an attribute of an entity <code>instructor</code>;
    * i.e. instructors have precisely one phone number each
* <code>phone</code> is an entity in its own right with attributes <code>phone_number</code>;
    * i.e. permits instructors to have several phone numbers (including zero) associated with them 

<p align = "center">
<img width="600" alt="Alternatives for adding phone to the instructor entity set" src="/assets/images/database-system/er-model/entity-set-vs-attribute.png">
<figcaption style="text-align: center;">$\mathbf{Fig\ 19.}$ Alternatives for adding phone to the instructor entity set. (<a href="#reference">Silberschatz et al.</a>) </figcaption>
</p><br>

The main difference is that treating a <code>phone</code> as an entity better models a situation where one may want to keep extra information about a <code>phone</code>, such as <code>location</code>, <code>type</code>, or possibly multiple <code>phone_number</code>s per <code>instructor</code>.

## Entity Sets vs. Relationship Sets

Consider the entity sets <code>section</code> and <code>student</code>, and the relationship between them indicating the section(s) that a student takes. There are two possible ways to model this as an E-R model:

* As a relationship set <code>takes</code>;
    * e.g. more compact and probably preferable

<p align = "center">
<img width="400" alt="takes relationship set" src="/assets/images/database-system/er-model/takes.png">
<figcaption style="text-align: center;">$\mathbf{Fig\ 20.}$ E-R diagram of <code>takes</code> relationship set (<a href="#reference">Silberschatz et al.</a>) </figcaption>
</p><br>

* As an entity set <code>registration</code>, and two relationship sets between <code>registration</code> and <code>section</code> or <code>student</code>;
    * e.g. if the registrar’s office associates other information with a course-registration record, it might be best to make it an entity in its own right.

<p align = "center">
<img width="600" alt="Replacement of takes by registration and two relationship sets" src="/assets/images/database-system/er-model/registration.png">
<figcaption style="text-align: center;">$\mathbf{Fig\ 21.}$ Replacement of <code>takes</code> by <code>registration</code> and two relationship sets. (<a href="#reference">Silberschatz et al.</a>) </figcaption>
</p><br>

A possible guideline is to designate a relationship set to describe an <strong>action</strong> that occurs between entities.

## Binary vs. Non-binary Relationships

Although it is possible to replace any non-binary ($n$-ary, $n > 2$) relationship set by a number of distinct binary relationship sets, a $n$-ary relationship sets shows more clearly that several entities participate in a single relationship.

However, some relationships that appear to be non-binary may be better represented using binary relationships.

* e.g. a ternary relationship <code>parents</code>, relating a child to his/her father and mother, is best replaced by two binary relationships, <code>father</code> and <code>mother</code>
    * Using two binary relationships allows partial information (e.g., only mother being known)
* But there are some relationships that are naturally non-binary
    * e.g., <code>proj_guide</code> 

### Converting Non-binary Relationships

In general, any non-binary relationship can be represented using binary relationships by creating an artificial entity set, and creating an identifying attribute for the artificial entity set to other entity sets participating in the relationship.

* Replace $R$ ($n = 3$) between entity sets $A$, $B$, and $C$ by an entity set $E$ and three relationship sets: 
    * $R_A$, many-to-one relating $E$ and $A$
    * $R_B$, many-to-one relating $E$ and $B$
    * $R_C$, many-to-one relating $E$ and $C$
    * $E$ is required to have total participation in each of $R_A$, $R_B$, and $R_C$ 
* Create an identifying attribute for $E$ and add any attributes of $R$ to $E$ 
* For each relationship $(a_i, b_i, c_i) \in R$, 
    * create a new entity $e_i$ in the entity set $E$
    * add $(e_i , a_i)$ to $R_A$
    * add $(e_i , b_i)$ to $R_B$
    * add $(e_i , c_i)$ to $R_C$

<p align = "center">
<img width="650" alt="Ternary relationship versus three binary relationships" src="/assets/images/database-system/er-model/binary-vs-non-binary.png">
<figcaption style="text-align: center;">$\mathbf{Fig\ 21.}$ Ternary relationship versus three binary relationships. (<a href="#reference">Silberschatz et al.</a>) </figcaption>
</p><br>

Thus, conceptually, we can restrict the E-R model to include only binary relationship sets. However, this restriction is not always desirable.

* Creation of new entity sets and attributes increases the complexity of the design and the overall storage requirements;
* An $n$-ary relationship set shows more clearly that several entities participate in a single relationship;
* There may not be a way to translate constraints on the ternary relationship into those on the binary relationships;
    * e.g., consider a constraint saying that $R$ is many-to-one from $A$, $B$ to $C$; that is, each pair of entities from $A$ and $B$ is associated with at most one $C$ entity. This constraint cannot be expressed by using cardinality constraints on $R_A$, $R_B$, and $R_C$
* There may be instances in the translated schema that cannot correspond to any instance of the original relationship;


<br><br><br>

# Reference
**[1]** [Silberschatz, Abraham, Henry F. Korth, and Shashank Sudarshan. "Database system concepts." (2011).](https://db-book.com/) <br>