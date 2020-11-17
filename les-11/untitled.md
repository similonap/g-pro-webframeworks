# Migrations

In real world projects, data models change as features get implemented: new entities or properties are added and removed, and database schemas needs to be changed accordingly to be kept in sync with the application. The migrations feature in EF Core provides a way to incrementally update the database schema to keep it in sync with the application's data model while preserving existing data in the database.

At a high level, migrations function in the following way:

* When a data model change is introduced, the developer uses EF Core tools to add a corresponding migration describing the updates necessary to keep the database schema in sync. EF Core compares the current model against a snapshot of the old model to determine the differences, and generates migration source files; the files can be tracked in your project's source control like any other source file.
* Once a new migration has been generated, it can be applied to a database in various ways. EF Core records all applied migrations in a special history table, allowing it to know which migrations have been applied and which haven't.

### Installing the tools

`dotnet ef` can be installed as either a global or local tool. Most developers prefer installing `dotnet ef` as a global tool using the following command:.NET Core CLICopy

```text
dotnet tool install --global dotnet-ef
```

Via nuget

```text
Microsoft.EntityFrameworkCore.Design (3.1.10)
```

### Create your first migration

You're now ready to add your first migration! Instruct EF Core to create a migration named **InitialCreate**:

```text
dotnet ef migrations add InitialCreate
```

EF Core will create a directory called **Migrations** in your project, and generate some files. It's a good idea to inspect what exactly EF Core generated - and possibly amend it - but we'll skip over that for now.

![](../.gitbook/assets/image%20%2882%29.png)

### Create your database and schema

Zorg ervoor dat je database gedeleted is!

At this point you can have EF create your database and create your schema from the migration. This can be done via the following:

```text
dotnet ef database update
```

That's all there is to it - your application is ready to run on your new database, and you didn't need to write a single line of SQL. Note that this way of applying migrations is ideal for local development, but is less suitable for production environments - see the [Applying Migrations page](https://docs.microsoft.com/en-us/ef/core/managing-schemas/migrations/applying) for more info.

