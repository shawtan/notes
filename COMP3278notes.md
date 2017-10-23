# Entity-Relationship Model

## Legend
- Rectangles: entity sets (table)
    - Double rectangle: weak entity set (table with column for primary key of identifying strong entity set)
- Ellipses: attributes of the entity (column in table)
    - Double ellipse: multi-valued attribute (separate table)
    - Dashed ellipse: derived attribute
    - Child ellipses: composite attributed (column in table)
- Diamonds: Relationship sets (between entities) 
    - Relational Table:
        - many-to-many: seperate table with primary keys of participating entity sets
        - many-to-one: if 'many' is total, add the primary key of the 'one'
        - one-to-one: use many-to-one, where either side can be the 'many'
    - Double diamond: identifying relationship 
- Triangle: Specialization relationship
    - Relational Table: either
        - Table containing primary key of the higher-level entity set and local attributes)
        - Table with all local and inherited attributes
            - PRO: Efficient data retrival
            - CON: Storage redundancy
- Directed Line (->): one
- Undirected Line (--): many
- Double line (=): total relationship
- Single line (-): partial relationship
- Underline: Primary key
    - Dotted underline: weak set identifier
- Disjoint label: disjoint specialization

## Entity and Entity set
- Entity: an object
    - e.g Customer, Account
- Attribute: something an entity has
    - e.g Customers have names
- Entity set: set of entities of the same type that share the same properties
    - Represents a table with the same attributes
    - e.g All Customers
- Relationship: association among entities
- Relationship set: set of relationships of the same type
    - Diamond with relationship type

## Constraints
### Mapping cardinalities
- Directed Line (->): one
- Undirected Line (--): many
  ![image](01_mapping_cardinalities.png)

### Participation constraints
- Total participaction (=): every entity participates in at least one relationship
- Partial participation (-): some entity may not participate in any relationship

## Keys
- Super key: set of one or more attributes that uniquely determine each entity
    - e.g {user_id, name}
- Candidate key: minimal (no subset super key exists) super key
    - e.g {user_id}
- Primary key (<u>underlined</u>): the candidate key selected to be the primary key

## Different Attribute Types
- Single vs Composite attributes (child ellipses)
    - Composite attributes are made of multiple attributes
    - e.g Address = {street, city, state}
- Single-valued vs Multi-valued attributes (double ellipses)
    - e.g Multiple phone numbers
    - Don't actually use this
- Derived attribute (dashed ellipses)
    - Values derived from other attributes
    - e.g age derived from date_of\_birth

## Weak Entity Set
- Represented by a double rectangle
- Entity set that does not have a primary key
- Must have a total, many-to-one identifying relationship set (double diamond) to the identifying entity set
- Discriminator (dotted underline): identifies entities within the weak set
- e.g Player's number is only unique within their team

## Role
- Relationship between entities in the same entity set
- Labeled with the different roles of the relationship
  ![image](01_role.png)

## Specialization
- Represented by a Triangle with relationship type (e.g ISA)
- Lower-level entity set that inherits all attributes and relationships of the higher level entity set
- Can also have its own attributes
- e.g Student can be full-time or part-time
- Disjoint vs Overlapping
    - Disjoint: entities can only belong to one specialization
      ![image](01_specialization.png)    

# Structured Query Language
- Language for defining, modifying and querying data
- Declarative (states what we want, not how)

## Data Definition
### Table Creation
~~~sql
CREATE TABLE table_name
(
    column1_name column_type CONDITIONS,
    ...
    CONSTRAINTS
);
~~~
- Conditions: `NOT NULL`
- Constraints: 
    - `PRIMARY KEY(column_name)`
    - `FOREIGN KEY(column_name) REFERENCES table_name(key)`

### Table Deletion
~~~sql
DROP TABLE table_name;
~~~
- Drop instruction may be rejected if table is referenced by other tables via constraints

### Table Modification
~~~sql
ALTER TABLE table_name ADD column_name;
ALTER TABLE table_name DROP column_name;
ALTER TABLE table_name ADD CONSTRAINT;
~~~

## Insert, Delete, Update
### Insert
~~~sql
INSERT INTO table_name VALUES
    (column1_value, column2_value, column3_value) -- row 1
    (column1_value, column2_value, column3_value) -- row 2
    ;
LOAD DATA LOCAL INFILE 'file.txt'
    INTO TABLE table_name
    FIELDS TERMINATED BY ','
    LINES TERMINATED BY '\n';    
~~~

### Delete
~~~sql
DELETE FROM table_name; -- Delete all records
DELETE FROM table_name WHERE column_name = 'test_value';
~~~

### Update
~~~sql
UPDATE table_name 
    SET column1_name = 'value'
    WHERE column2_name = 'test_value';
UPDATE table_name
    SET column1_name = column1_name * 10;
UPDATE table_name
    SET column_name = CASE
        WHEN column_name <=500 THEN column_name * 1.05
        ELSE column_name * 1.06
        END;
~~~

## Querying
