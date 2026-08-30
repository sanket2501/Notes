# EF Core

Tags: dotnet, efcore, csharp

This folder contains practical EF Core notes based on the source PDF. The content focuses on ORM basics, database-first and code-first approaches, DbContext configuration, entity relationships, raw SQL, stored procedures, and common async pitfalls in .NET applications.

## Topics

- [01 ORM and Setup.md](01%20ORM%20and%20Setup.md)
- [02 Database First Approach.md](02%20Database%20First%20Approach.md)
- [03 Code First Approach.md](03%20Code%20First%20Approach.md)
- [04 DbContext Configuration and Relationships.md](04%20DbContext%20Configuration%20and%20Relationships.md)
- [05 Raw SQL, Stored Procedures, and Async Errors.md](05%20Raw%20SQL,%20Stored%20Procedures,%20and%20Async%20Errors.md)

## Learning Path

1. Understand what ORM is and why EF Core is a strong choice for .NET apps.
2. Learn how to set up connection strings and database access with SQL Server.
3. Work with Database First design using scaffolding.
4. Move to Code First with migrations and model design.
5. Configure DbContext, relationships, and validation rules.
6. Query data with LINQ, raw SQL, and stored procedures.
7. Apply async best practices and avoid common EF Core lifecycle mistakes.

## Core Areas Covered

- ORM concepts and comparison
- Connection string configuration in appsettings, Key Vault, and Docker
- EF Core installation and setup
- Database First and Code First workflows
- DbContext, models, and migrations
- Data annotations and Fluent API configuration
- Relationship mapping: one-to-one, one-to-many, many-to-many
- Raw SQL execution and stored procedures
- Async usage and `DbContext` disposal issues
- Real-world C# examples and practical patterns
