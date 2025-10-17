The assignment is making a clean Data Layer (class library) using EF Core over a small Northwind DB that exposes 13 operations across Orders, OrderDetails, Products, Categories, so they are ready to be used by a REST layer. 

#### Entity Framework Core — OnModelCreating & Mapping

`OnModelCreating` is where we tell Entity Framework (EF) Core how our C# classes match the actual database structure.

It is used for:
1. Mapping class and property names to real database tables and columns.
2. Defining relationships (one-to-many, many-to-many)
3. Declaring primary keys, including composite keys (where two columns form one key)

Without it, EF tries to guess names — but databases like Northwind often use lowercase names or different column names, which can cause runtime errors. 

##### Why is Explicit Mapping Important?
In the Northwind database, the tables and columns are named differently from your C# classes. 

Example 
> Class: `Category.Id` → Database column: `categoryid` 
> Class: `Product.Name` → Database column: `productname`

If we don't tell the EF about these differences, it will look for "Id" instead of "categoryid" and fail to connect correctly. 

e.g. on explicit mappings
```C#
modelBuilder.Entity<Category>().ToTable("categories");
modelBuilder.Entity<Category>().Property(x => x.Id).HasColumnName("categoryid");
```

Here we make sure that the EF talks to the correct table in the column. 

#### Key Learning Points

###### 1. Table and column mapping
`ToTable()` and `HasColumnName()` ensures that the entity model matches the database schema exactly. 

Think:
Class name →  Table name 
Property → Column name

###### 2. Relationships
Use .HasMany() and .WithOne() to define how entities relate

```C#
modelBuilder.Entity<Category>()
    .HasMany<Product>()
    .WithOne(p => p.Category)
    .HasForeignKey(p => p.CategoryId);
```

This creates a one-to-many relationship (one category can have many prodcuts).

###### 3. Composite keys
Some tables like `OrderDetail` don't have a single ID column. They use two columns together as a unique key:

```C#
modelBuilder.Entity<OrderDetail>()
    .HasKey(od => new { od.OrderId, od.ProductId });
```

###### 4. Case sensitivity & naming
PostgreSQL is case-sensitive when using quoted identifiers.
Mapping to lowercase table/column names avoids many "column does not exist" errors. 

###### 5. Why both mappings and relationships matter

1. Mappings let EF know what tables and columns to use
2. Relationships tell EF how those tables connect.

