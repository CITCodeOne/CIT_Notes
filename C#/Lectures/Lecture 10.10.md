Today was about the three common strategies for modeling class inheritance in relational databases and introduces low-level database access in C# using ADO.NET. 

There is highlights about the challenges of the 'object-relational impedance mismatch' and how Object-Relational Mapping (ORM) tools like Entity Framework provide a higher-level abstraction to manage this complexity enabling LINQ queries against database.

**Learning Objectives**

- To understand Entity Framework for database interaction.
- Practical coding examples for database operations.
- Introduction to the second assigments using abstractions like ORM and ASP.NET

## 1. Object-Relational Impedance Mismatch

The fundamental differences between the object-oriented (OO) programming paradigm and relational database model. 

**Key Differences**

|                | Object-Oritented                                                                                                 | Relational Database Model                                                                    | Notes                                                     |
| -------------- | ---------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------- | --------------------------------------------------------- |
| Data Structure | Objects, classes, collections, references                                                                        | Tables (relations), rows (tuples), columns (attributes), foreign keys.                       |                                                           |
| Relationships  | Direct object references (memory pointers).<br><br>Many-to-Many: often represented by collections within objects | Primary and Foreign keys<br><br><br>Many-to-Many: requires a junction table.                 |                                                           |
| Inheritance    | Classes inherit properties and methods from parent classes                                                       | No direct equivalent; must be simulated through design patterns. (read section 2).           |                                                           |
| Data Types     |                                                                                                                  |                                                                                              | Differences in how types are represented and constrained. |
|                |                                                                                                                  |                                                                                              | Often differ between camelCase and snake_case.            |
|                |                                                                                                                  | Has built-in constraints (e.g. foreign key constraints) that are not native to OO languages. |                                                           |


## 2. Database Inheritance Strategies.
There are three main ways to represent class inheritance hierarchies in a relational database, which all have pros and cons.

1. One Table for the Full Hierarchy
	This approach involves creating single table in the database to store all attributes from all classes within a hierarchy (e.g. `User`, `Teacher`, `Student`).
	
	**Implementation**
	- All fields from all classes (parent and subclasses) are combined into one table.
	- A discriminator column (e.g., `user_type`) is added to distinguish between instances of different classes (e.g. `Teacher` or `Student`)
	- Null values will be present for attributes not relevant to a specific subclass (e.g. a `Teacher row` will have `NULL` for `student_specific_attribute`).
	 
	 **Pros:**
	 - **Fast reads**: All data for any instance is in one place, requiring no joins.
	 - Simple for basic counting of all users/entities in the hierarchy.
	 - Performance is fast for queries involving multiple types or the base type, as all data is in one table (no joins needed).
	 
	 **Cons**
	 - **Data Sparsity**: Can lead to a large number of `NULL`s, especially with deep or wide hierarchies. 
	 - **Wider tables**: The table grows horizontally with more columns as the hierarchy expands, potentially impacting performance and storage efficiency.
	 - **Data integrity issues**: Can violet "not null" constraints if certain company policies (like IBM's) disallow NULL values in database.
	 - **Data Integrity**: Less strict integrity for subclass-specific data without additional constraints.
2. One Table per Class in the Hierarchy (Table per Type / Joined Subclass Strategy)
	Each class including abstract base classes in the hierarchy gets its own dedicated database table.
	
	**Implementation**
	- Tables are linked using foreign keys
	- A subclass table typically holds its specific attributes and a foreign key reference to its parent class's table (often also serving as its primary key).
	
	**Example**
	`User` table (common attributes), `Teacher` table (teacher specific attributes + FK to `User`) `Student` table (student specific attributes + FK to `User`).
	
	**Pros**
	- **Normalization**: Reduced data redundancy.
	- **Data Integrity**: Fewer NULL values, as each table only stores relevant attributes for its class.
	- **Clarity**: Database schema directly reflects the class hierarchy.
	
	**Cons**
	- **Performance**: Queries often require JOIN operations across multiple tables to retrieve complete object data, which can be slower than querying a single table.
	
	  > e.g. To get a `Teacher`'s full profile (including `User` details), a join between `Teacher`and `User` table is needed.
	  
	- **Complexity**: More complex queries due to joins.
3. One Table for Concrete Types Only (Table per Concrete Class Strategy)
	Only concrete (instantiable) classes in the hierarchy get a database table. Abstract classes do not have corresponding tables.
	
	**Implementation**
	- Each concrete class's table contains all its attributes, including inherited ones, effectively duplicated parent class attributes across concrete subclass tables.
	- No shared parent table for abstract types.
	
	**Reason**
	>Abstract classes cannot be instantiated, so there's no data instance to store directly in
	>the database.
	
	**Pros**
	- No `NULL` values, as the table is specific to a concrete type.
	- No joins needed for a single concrete objects data.
	
	**Disadvantages**
	- **Data Duplication**: Common attributes inherited from abstract parents are duplicated across mulitple tables.
	- **Maintenance**. Changes to common attributes require updates across multiple talbes.
	- **Querying Base Type**: Querying all "users" (if `User` was abstract) would require and uniting results from all concrete subclass tables (e.g. `Teacher`, `Student`).


## 3. Object-Relational Mapping (ORM) Tools
The purpose with ORm is to bridge the impedance mismatch by abstracting database interactions and allowing developers to work with objects.

**Benefits**
1. Productivity: Reduces boilerplate code (e.g. SQL queries, data hydration).
2. Type Safety:  Provides compile-time checking instead of runtime SQL errors.
3. Abstraction: Hides underlying database specifics, allowing focus on domain logic.
4. Change Tracking: Automatically detects changes in objects and generates appropriate update queries.
5. Data Type Translation: Handles conversion between database and programming language data types (e.g. `VARCHAR`to `string`, various data/time formats).
6. Relationship Management: Simplifies handling of one-to-one, one-to-many, and many-to-many relationships. 

**Risks**
1. Performance: Can generate inefficient SQL queries if not used carefully or understood deeply.
2. Abstraction Leakage: Developers may lose understanding of underlying database operations leading to suboptimal design or queries. 
3. "Shitty models": Over-reliance on ORM abstractions without proper database design can be lead to poor models in both paradigms. 
4. Overhead: Tracking changes and providing features adds some performance overhead. 

**ORM Architectures/Mapping Approaches**
1. Domain Classes with Built-in Database Knowledge: Object classes contain metadata or code related to their database mapping. (e.g. annotations/attributes).
2. Separate Mapping Layer: Domain classes are pure object-oriented, with no database knowledge. A separate mapping layer (configuration) defines how objects map to database tables and columns. This promotes separation of concerns. *This is preferred by Entity Framework Core*.

## 4. ADO.NET (ActiveX Data Objects .NET)

The low-level framework in C# for connecting and interacting with databases. Analogous to JDBC in java.

**Core Concepts**
1. Connection: Establishes a connection to the database (e.g. `NpsqlConnection` for PostgreSQL). This requires a connection string (server, database name, user, password).
2. Command: Executes SQL statements against the database, e.g. `NpgsqlCommand`

```SQL
ExecuteNonQuerry(): /* Is used for DML and DDL statements */
DML (INSERT, UPDATE, DELETE)
DDL (CREATE TABLE)

ExecuteReader(): /* For queries that return a result set e.g. SELECT. Returns a DataReader */

ExecuteScalar(): /* For queries that return a single value */
```
 
3. DataReader: Provides a forward only, read-only cursor to access query results row by row. 

**Performance Considerations**
1. Round Trips: Communication with external systems like databases involves network round trips, which are expensive. 
2. Boilerplate Code: Requires manual writing of SQL, connection handling and data mapping. 
3. Not Object-Oriented: Directly manipulates tabular data.

## 5. Entity Framework

A popular Object-Relational Mapper (ORM) framework for .NET applications.

**Background** 
1. Original EF: Developed for Windows platform, grew into a "monster" with many features becoming slower. 
2. EF Core: Rewritten from scratch for .NET Core (cross-platform). Focused on performance and a more steamlined architecture. Many older features were dropped for speed.
3. Current: Now simply called "Entity Framework" (referring to EF Core).


