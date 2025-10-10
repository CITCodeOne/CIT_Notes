This document outlines three common strategies for modeling class inheritance in relational databases and introduces low-level database access in C# using ADO.NET. It highlights the challenges of the "object-relational impedance mismatch" and how Object-Relational Mapping (ORM) tools like Entity Framework provide a higher-level abstraction to manage this complexity, enabling LINQ queries against databases.

Database Inheritance Strategies

There are three main ways to represent class inheritance hierarchies in a relational database, each with pros and cons:

1. One Table for the Full Hierarchy (**Table per Hierarchy / Single Table Inheritance**)

• **Concept**: A single database table stores data for all classes within an inheritance hierarchy.

• **Implementation**:

• Includes all attributes (columns) from all classes in the hierarchy.

• Requires a _discriminator column_ (e.g., UserType) to identify the actual class of each row (e.g., 'Teacher', 'Student').

• Attributes not relevant to a specific class instance will have NULL values.

• **Example**: User base class with Teacher and Student subclasses. One Users table with columns for User properties, Teacher properties, and Student properties.

• **Advantages**:

• **Performance**: Fast for queries involving multiple types or the base type, as all data is in one table (no joins needed).

• **Disadvantages**:

• **Data Sparsity (NULLs)**: Leads to many NULL values, especially with complex hierarchies, as each row only populates relevant subclass attributes.

• _Policy Issue_: Some companies (e.g., IBM mentioned) may disallow NULL values in databases.

• **Table Size**: Table can become very "wide" (many columns) and large, potentially impacting performance and manageability.

• **Data Integrity**: Less strict integrity for subclass-specific data without additional constraints.

2. One Table per Class in the Hierarchy (**Table per Type / Joined Subclass Strategy**)

• **Concept**: Each class (including abstract base classes) in the hierarchy gets its own dedicated database table.

• **Implementation**:

• Tables are linked using _foreign keys_.

• A subclass table typically holds its specific attributes and a foreign key reference to its parent class's table (often also serving as its primary key).

• **Example**: User table (common attributes), Teacher table (teacher-specific attributes + FK to User), Student table (student-specific attributes + FK to User).

• **Advantages**:

• **Normalization**: Reduced data redundancy.

• **Data Integrity**: Fewer NULL values, as each table only stores relevant attributes for its class.

• **Clarity**: Database schema directly reflects the class hierarchy.

• **Disadvantages**:

• **Performance**: Queries often require _JOIN operations_ across multiple tables to retrieve complete object data, which can be slower than querying a single table.

• _Example_: To get a Teacher's full profile (including User details), a join between Teacher and User tables is needed.

• **Complexity**: More complex queries due to joins.

3. One Table for Concrete Types Only (**Table per Concrete Class Strategy**)

• **Concept**: Only concrete (instantiable) classes in the hierarchy get a database table. Abstract classes do not have corresponding tables.

• **Implementation**:

• Each concrete class's table contains all its attributes, including inherited ones, effectively duplicating parent class attributes across concrete subclass tables.

• No shared parent table for abstract types.

• **Reason**: Abstract classes cannot be instantiated, so there's no data instance to store directly in the database.

• **Advantages**:

• No NULL values, as each table is specific to a concrete type.

• No joins needed for a single concrete object's data.

• **Disadvantages**:

• **Data Duplication**: Common attributes inherited from abstract parents are duplicated across multiple tables.

• **Maintenance**: Changes to common attributes require updates across multiple tables.

• **Querying Base Type**: Querying all "users" (if User was abstract) would require querying and uniting results from all concrete subclass tables (e.g., Teacher, Student).

Database Access with ADO.NET and ORM

Learning Objectives

• Understand _Entity Framework_ for database interaction.

• Practical coding examples for database operations.

• Introduction to the second assignment, using abstractions like ORM and ASP.NET.

Example Database: The Northwind Database

• **Origin**: Created in the _1990s_ as a sample database for Microsoft SQL Server.

• **Focus**: Orders and related entities.

• **Key Entities**:

• **Orders**: Main entity.

• **Customers**: Place orders.

• **Order Details**: Links Orders to Products, specifying quantities.

• **Products**: Items in orders.

• **Categories**: Group products.

• **Suppliers**: Provide products.

• **Example Relationships**:

• **Many-to-Many**: _Products_ and _Orders_ (via Order Details join table).

• **One-to-Many**: _Category_ to _Products_ (one product has one category, one category can have many products).

• **Setup**:

• Provided as a small SQL script (downloadable).

• Requires a local PostgreSQL instance.

• **Importance of Local DB**: Provides a faster feedback loop during development compared to remote servers (avoids VPN, network latency).

• **Usage**: All class samples and assignments will use this database.

Connecting C# to Databases: LINQ and the Need for ADO.NET/ORM

• **LINQ (Language Integrated Query)**: A C# feature for querying collections (like arrays, lists). It uses syntax similar to SQL.

• **Challenge**: LINQ itself does not inherently understand databases. It needs a mechanism to translate C# queries into database-specific commands (e.g., SQL) and execute them.

ADO.NET: Low-Level Database Connectivity

• **Definition**: Microsoft's framework for interacting with databases from .NET applications.

• **Analogy**: Similar to _JDBC_ (Java Database Connectivity) in Java.

• **Architecture**:

• **Interface**: ADO.NET defines a standard interface.

• **Providers**: Database vendors (PostgreSQL, MySQL, SQL Server, Oracle) create specific _implementations_ of this interface (e.g., Npgsql for PostgreSQL).

• **Core Concepts/Steps for Database Communication**:

1. **Connection**:

• Establish a communication channel to the database.

• Requires a _connection string_ (server address, database name, user credentials, password).

• NpgsqlConnection for PostgreSQL.

• Authentication checks (e.g., wrong password will cause an error).

2. **Command**:

• Define the SQL statement or query to be executed.

• NpgsqlCommand for PostgreSQL.

• Set CommandText property to the SQL query (e.g., SELECT * FROM Categories).

3. **Execution and Result Handling**:

• ExecuteNonQuery(): For SQL statements that don't return data (e.g., INSERT, UPDATE, DELETE, CREATE TABLE). Returns an integer (e.g., number of rows affected).

• ExecuteScalar(): For queries that return a single, scalar value (e.g., COUNT(*), SUM()).

• ExecuteReader(): For SELECT queries that return multiple rows and columns. Returns a DataReader (e.g., NpgsqlDataReader).

• The DataReader works like a _cursor_, allowing row-by-row iteration (reader.Read()).

• Data can be accessed by column index or name (e.g., reader.GetString(1) for the second column).

Installing Database Providers (e.g., Npgsql)

• **NuGet**: .NET's package manager system.

• **Process**:

1. Use Visual Studio's "Manage NuGet Packages" feature (or command line).

2. Search for the desired database provider package (e.g., Npgsql).

3. Consider packages with a high number of downloads (e.g., >_5,000_ is a rough guideline for widely-used, reliable packages). Npgsql has over _600 million_ downloads.

4. Install the package; NuGet handles dependencies (e.g., Npgsql depends on System.Text.Json).

5. Once installed, the provider's classes (e.g., NpgsqlConnection) become available in the project.

Problems with Low-Level ADO.NET / Raw SQL

1. **Lack of Compile-Time Error Checking**:

• SQL syntax errors or invalid table/column names in CommandText are _runtime errors_.

• The C# compiler cannot validate the SQL, making debugging harder.

2. **Object-Relational Impedance Mismatch**:

• **Core Issue**: Fundamental differences between the _relational model_ (databases) and the _object-oriented model_ (programming languages).

• **Relational Model Concepts**: Tables, rows, columns, primary keys, foreign keys, constraints.

• **Object-Oriented Model Concepts**: Classes, objects, properties, methods, inheritance, references.

• **Specific Mismatches**:

• **Foreign Keys vs. Object References**: Relational foreign keys enforce integrity. Object references are memory pointers without inherent database-level constraints.

• **Inheritance**: Object-oriented languages directly support inheritance. Relational databases do not; they require specific mapping strategies (like the three described above).

• **Collections in Objects**: Objects can have properties that are collections (e.g., List<Order>). Relational database columns can only hold single values.

• _Many-to-Many Relationships_: In OO, represented by lists of objects on both sides. In relational, requires a separate _join/intermediate table_ (e.g., BookCategory table between Book and Category).

Database Communication Costs

• **Round-Trips are Expensive**: Each interaction (sending a query, receiving data) between the client application and the database server involves network communication (TCP connection). This overhead can be significant, especially over a network.

• **Optimization (Driver Level)**: Database drivers (like Npgsql) optimize by fetching data in chunks (e.g., _50 rows_ at a time) and caching them locally. This reduces the number of round-trips, improving perceived performance even when iterating row-by-row.

Object-Relational Mapping (ORM)

• **Purpose**: Tools (like _Entity Framework_) designed to bridge the object-relational impedance mismatch.

• **Functionality**:

• Translates LINQ queries (C# objects) into database-specific SQL queries.

• Maps database results back to C# objects.

• Handles common relational concepts (relationships, primary/foreign keys) and represents them in an object-oriented way.

• **Benefits**:

• Higher-level abstraction: Developers work with objects, not raw SQL.

• Compile-time checks: Many issues (e.g., non-existent properties) are caught at compile time, not runtime.

• Reduced boilerplate code for data access.

• Improved maintainability.

• **Trade-off**: May not be as fast as highly optimized raw SQL for extreme performance-critical scenarios but offers significant development productivity gains.