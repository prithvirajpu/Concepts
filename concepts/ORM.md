# ORM Notes (Django ORM)

> A clean layer in Django that maps Python objects to database tables, so you can work with data using models, querysets, and relationships instead of writing raw SQL for everything. [web:61][web:63]

## Table of Contents
- [ORM Notes (Django ORM)](#orm-notes-django-orm)
  - [Table of Contents](#table-of-contents)
  - [What is ORM?](#what-is-orm)
  - [Why use ORM?](#why-use-orm)
  - [Key Features](#key-features)
  - [Core Concepts](#core-concepts)
  - [Basic Architecture](#basic-architecture)
  - [Where is ORM Used?](#where-is-orm-used)
  - [Real-World Applications](#real-world-applications)
  - [How Django ORM Works](#how-django-orm-works)
  - [Common Query Methods](#common-query-methods)
  - [Relationships in ORM](#relationships-in-orm)
    - [One-to-One](#one-to-one)
    - [One-to-Many](#one-to-many)
    - [Many-to-Many](#many-to-many)
    - [Foreign Key](#foreign-key)
  - [Migrations and Schema Changes](#migrations-and-schema-changes)
  - [ORM vs Raw SQL](#orm-vs-raw-sql)
  - [Best Practices](#best-practices)
  - [Example – Ticket Model](#example--ticket-model)
    - [Example queries](#example-queries)

## What is ORM?

**ORM** stands for **Object Relational Mapping**. It is a technique that maps **Python objects** to **database tables** and object fields to table columns. [web:61][web:63]

- A Django **model class** represents a table.
- A model **instance** represents a row.
- A model **field** represents a column.

In short:
- **Python object** → database row.
- **Model class** → database table.
- **Django ORM** → translates Python code into SQL automatically. [web:61][web:63]

## Why use ORM?

- **Less SQL writing**: You can create, read, update, and delete data using Python code. [web:63][web:69]
- **Faster development**: Common database operations are simpler and shorter. [web:61][web:63]
- **Cleaner code**: Business logic stays more readable than raw query-heavy code.
- **Safer by default**: ORM helps reduce manual SQL string building.
- **Easier maintenance**: Model-based code is easier to refactor and organize.
- **Works well with Django**: Models, migrations, querysets, and admin all connect naturally. [web:67][web:70]

You can still use raw SQL when needed, but ORM is usually the first choice for normal app development. [web:69][web:70]

## Key Features

- Model classes for tables.
- Querysets for fetching and filtering data. [web:68][web:69]
- CRUD operations through Python methods.
- Relationship support: one-to-one, one-to-many, many-to-many. [web:61][web:65][web:67]
- Lazy evaluation of queries.
- Migrations for database schema changes. [web:64][web:70]
- Field validation and constraints through model definitions. [web:67]

## Core Concepts

- **Model**:
  - A Python class that defines a database table.
  - Example: `Ticket`, `User`, `Order`.

- **Field**:
  - A column inside the table.
  - Example: `CharField`, `IntegerField`, `DateTimeField`. [web:67]

- **QuerySet**:
  - A collection of database records returned by ORM queries.
  - Can be filtered, ordered, chained, and further refined. [web:68][web:69]

- **Manager**:
  - The interface used to access query operations.
  - Example: `Ticket.objects`.

- **Relationship**:
  - Links between models using `ForeignKey`, `OneToOneField`, and `ManyToManyField`. [web:61][web:65]

- **Migration**:
  - A file that stores schema changes and applies them to the database. [web:64][web:70]

## Basic Architecture

1. You define a model in `models.py`.
2. Django creates or updates the table structure through migrations. [web:64][web:70]
3. Your app uses `Model.objects` to run ORM queries.
4. Django converts those ORM calls into SQL.
5. The database returns results.
6. Django converts the results back into Python objects. [web:61][web:63]

This gives you a high-level way to work with relational data without manually writing every SQL statement. [web:63][web:69]

## Where is ORM Used?

- Django web applications.
- REST APIs built with Django or DRF.
- Admin panels and dashboards.
- Ticketing systems and CRM apps.
- E-commerce backends.
- Internal business tools.
- Data-driven applications with lots of CRUD operations. [web:61][web:63][web:69]

## Real-World Applications

1. **Ticketing systems**
   - Tickets, assignments, agents, activity logs.
   - ORM is useful for creating and updating ticket states.

2. **E-commerce**
   - Products, carts, orders, payments.
   - ORM helps manage relationships between users and orders.

3. **User management**
   - Profiles, roles, permissions, login sessions.

4. **Inventory systems**
   - Stock items, warehouse records, supplier relations.

5. **Analytics dashboards**
   - Reports, metrics, aggregates, and filters.

ORM is especially helpful when your app has many connected tables and frequent CRUD operations. [web:61][web:69]

## How Django ORM Works

1. You write a model.
2. Django maps that model to a database table.
3. You call methods like `.all()`, `.filter()`, `.get()`, or `.create()`. [web:62][web:68]
4. Django builds SQL behind the scenes.
5. The database executes the query.
6. Django gives you back Python objects or query results. [web:61][web:63]

Example:
```python
Ticket.objects.filter(status="OPEN")
```

This means:
- go to the `Ticket` table,
- find rows where `status = "OPEN"`,
- return them as a queryset. [web:62][web:68]

## Common Query Methods

- `.all()` → get all records.
- `.filter()` → get records matching conditions.
- `.get()` → get one record.
- `.exclude()` → remove matching records.
- `.create()` → insert a new record.
- `.update()` → change existing records.
- `.delete()` → remove records.
- `.order_by()` → sort results.
- `.values()` → return dictionaries instead of model instances.
- `.distinct()` → remove duplicates. [web:62][web:68][web:69]

Example:
```python
Ticket.objects.filter(status="OPEN").order_by("-created_at")
```

This fetches all open tickets and sorts them newest first.

## Relationships in ORM

### One-to-One
One record in one table matches exactly one record in another table.

### One-to-Many
One parent record can have many child records.
Example: one `User` can have many `TicketAssignment` rows. [web:61][web:65]

### Many-to-Many
Many records in one table can relate to many records in another table.
Example: users and groups, products and tags. [web:67][web:69]

### Foreign Key
This is the most common relationship in Django ORM.
Example:
```python
class TicketAssignment(models.Model):
    ticket = models.ForeignKey(Ticket, on_delete=models.CASCADE)
```

This means many assignments can belong to one ticket.

## Migrations and Schema Changes

When you change a model, Django uses migrations to update the database structure. [web:64][web:70]

Typical commands:
```bash
python manage.py makemigrations
python manage.py migrate
```

Why migrations matter:
- they keep schema changes versioned,
- they let teams apply database updates safely,
- they make model changes repeatable across environments. [web:64][web:70]

## ORM vs Raw SQL

| ORM | Raw SQL |
|---|---|
| Easier to write | More manual work |
| Better for standard CRUD | Better for advanced tuning |
| More readable | More control |
| Integrated with Django models | Direct database access |

Use ORM for normal application logic. Use raw SQL when you need special performance tuning, complex reporting, or database-specific behavior. [web:69][web:70]

## Best Practices

- Use ORM for normal CRUD operations.
- Keep models clean and meaningful.
- Use relationships properly instead of duplicating data.
- Avoid too many queries inside loops.
- Learn how querysets behave before optimizing.
- Use raw SQL only when ORM is not enough.
- Understand the SQL being generated for important queries. [web:62][web:69][web:70]

## Example – Ticket Model

```python
from django.db import models

class Ticket(models.Model):
    title = models.CharField(max_length=200)
    description = models.TextField()
    status = models.CharField(max_length=50)
    created_at = models.DateTimeField(auto_now_add=True)
```

### Example queries
```python
Ticket.objects.create(
    title="Login issue",
    description="User cannot log in",
    status="OPEN"
)
```

```python
Ticket.objects.filter(status="OPEN")
```

```python
Ticket.objects.get(id=1)
```

```python
Ticket.objects.filter(status="OPEN").update(status="IN_PROGRESS")
```

This is the standard Django ORM workflow: define the model, migrate, query with `objects`, and let Django handle SQL translation. [web:61][web:63][web:70]