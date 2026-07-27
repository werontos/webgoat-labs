Lauch WebGoat

`sudo docker run -p 127.0.0.1:8080:8080 -p 127.0.0.1:9090:9090 -e TZ=Europe/Amsterdam webgoat/webgoat`

## SQL Injection
<img width="1414" height="842" alt="image" src="https://github.com/user-attachments/assets/3f9b5fbe-27be-4885-9543-b97a70b47f49" />

So, here we have information bout SQL:
A company saves the following employee information in their databases: a unique employee number ('userid'), last name, first name, department, salary and a transaction authentication number ('auth_tan'). Each of these pieces of information is stored in a separate column and each row represents one employee of the company.

SQL queries can be used to modify a database table and its index structures and add, update and delete rows of data.

There are three main categories of SQL commands:

    Data Manipulation Language (DML)

    Data Definition Language (DDL)

    Data Control Language (DCL)

Each of these command types can be used by attackers to compromise the confidentiality, integrity, and/or availability of a system. Proceed with the lesson to learn more about the SQL command types and how they relate to protections goals.

<img width="525" height="230" alt="image" src="https://github.com/user-attachments/assets/ef1c5e95-8fef-486b-96c5-18efe3db3eee" />


Out task here is to retrieve the department of the employee Bob Franco from that Table named Employees,
So, Our SQL Query should look like this
SELECT department FROM Employees WHERE first_name = 'Bob' AND last_name = 'Franco';
Lemme break down this line for you
SELECT - we choose what we want to get from the table
FROM - from where this data should come from(table Employees)
WHERE - its simple english so here is nothing to breakdown
first_name - is our column = 'Bob', so we basically tell sql to find Bob in the first_name column
last_name - same as previous one but here we tell sql to find Franco in the last_name column
<img width="785" height="292" alt="image" src="https://github.com/user-attachments/assets/0cfc88c0-3fc8-4cf7-ad60-6c4bc30146b7" />

And here is our data = Marketing
<img width="1356" height="713" alt="image" src="https://github.com/user-attachments/assets/5e857378-e5bf-4e7b-aa00-9d7d8c975838" />

Second task related with DML (Data Manipulation Language)


If an attacker succeeds in "injecting" DML statements into a SQL database, he can violate the confidentiality (using SELECT statements), integrity (using UPDATE statements), and availability (using DELETE or UPDATE statements) of a system.

    DML commands are used for storing, retrieving, modifying, and deleting data.

    SELECT - retrieve data from a database

    INSERT - insert data into a database

    UPDATE - updates existing data within a database

    DELETE - delete records from a database

    Example:

        Retrieve data:

        SELECT phone
        FROM employees
        WHERE userid = 96134;

        This statement retrieves the phone number of the employee who has the userid 96134.


Here we need change the department of Tobi Barnett to 'Sales'. Note that you have been granted full administrator privileges in this assignment and can access all data without authentication.

<img width="1166" height="185" alt="image" src="https://github.com/user-attachments/assets/733fefbd-6df3-4b8e-871c-22ceb09f8060" />

So, out SQL Query should look like this
UPDATE employees SET department = 'Sales' WHERE first_name = 'Tobi' AND last_name = 'Barnett';
Here is the breakdown of query
UPDATE - updates existing data in employees 
SET - is a command to change the department "job"
Where - its simple english so here is nothing to breakdown
and last 2 is the same as previous
<img width="1144" height="314" alt="image" src="https://github.com/user-attachments/assets/d14ceacf-f7d5-455f-993e-755fafb61f73" />

Move further
<img width="1358" height="701" alt="image" src="https://github.com/user-attachments/assets/1e8ad8c4-e09a-4f19-8601-f18713025e17" />

Here we got a DDL(Data Definition Language)
If an attacker successfully "injects" DDL type SQL commands into a database, he can violate the integrity (using ALTER and DROP statements) and availability (using DROP statements) of a system.

    DDL commands are used for creating, modifying, and dropping the structure of database objects.

    CREATE - create database objects such as tables and views

    ALTER - alters the structure of the existing database

    DROP - delete objects from the database

    Example:

        CREATE TABLE employees(
            userid varchar(6) not null primary key,
            first_name varchar(20),
            last_name varchar(20),
            department varchar(20),
            salary varchar(10),
            auth_tan varchar(6)
        );

Now our task is 
<img width="672" height="144" alt="image" src="https://github.com/user-attachments/assets/da2a6b0d-12e2-4442-8388-49ff3f0a7438" />

So, we need to add a "phone" column to the table employees
Here is the SQL Query
`ALTER TABLE employees ADD phone varchar(20);`
ALTER - alter the structure of the EXISTING TABLE which is employees
ADD - simply add what we need in our case phone with varchar(20)
<img width="501" height="183" alt="image" src="https://github.com/user-attachments/assets/05e2fff9-a2b8-48f2-aae8-924278bb875e" />

Moving further we are facing the DCL(Data Control Language)
<img width="1390" height="607" alt="image" src="https://github.com/user-attachments/assets/0b1ae562-5af7-48a2-9518-a4d45e53a01d" />


If an attacker successfully "injects" DCL type SQL commands into a database, he can violate the confidentiality (using GRANT commands) and availability (using REVOKE commands) of a system. For example, the attacker could grant himself admin privileges on the database or revoke the privileges of the true administrator.

    DCL commands are used to implement access control on database objects.

    GRANT - give a user access privileges on database objects

    REVOKE - withdraw user privileges that were previously given using GRANT

<img width="578" height="170" alt="image" src="https://github.com/user-attachments/assets/8dbde2ad-49a6-4c7d-bc81-062be6c8c616" />

We need to grant rights to the grant_rights table to use unauthorized_user
Here is our SQL Query
GRANT ALL ON grant_rights TO unauthorized_user;
GRANT - give some1 access privileges on database objects
ON - is a table on which we will grant privileges
TO - a specific user
<img width="512" height="173" alt="image" src="https://github.com/user-attachments/assets/f39f8843-5044-4647-840a-d415d850efe5" />

So tasks 6,7,8 is explaining to us what is a SQL Injection but i hope you already understand what is it here is a SUPER CUTTED VERSION Of 6,7,8
# What is SQL Injection?

**SQL Injection (SQLi)** is one of the most common web vulnerabilities. It occurs when user input is inserted into an SQL query without proper validation or parameterization, allowing an attacker to manipulate the query.

Applications should always use **prepared statements** (parameterized queries) and validate user input to prevent SQL injection.

## Example

The application builds the following query:

```sql
SELECT * FROM users WHERE name = '" + userName + "';
```

If the user enters:

```text
Smith
```

The query becomes:

```sql
SELECT * FROM users WHERE name = 'Smith';
```

However, if an attacker enters:

```text
Smith' OR '1'='1
```

The query becomes:

```sql
SELECT * FROM users WHERE name = 'Smith' OR '1'='1';
```

Since the condition is always true, the database returns all records.

## Examples

Some common SQL injection payloads:

```text
Smith' OR '1'='1
```

Returns every row in the table.

```text
Smith' OR 1=1; --
```

Bypasses the original condition and ignores the rest of the query.

```text
Smith'; DROP TABLE users; --
```

Attempts to execute an additional SQL command to delete the table (if multiple statements are allowed).

## Consequences of SQL Injection

A successful SQL injection attack may allow an attacker to:

- Read or modify sensitive data.
    
- Bypass authentication.
    
- Delete or alter database tables.
    
- Execute administrative database operations.
    
- In some DBMSs, execute operating system commands.
    

## Severity of SQL Injection

The impact depends on:

- The attacker's skill.
    
- Database permissions (least privilege).
    
- Input validation and parameterized queries.
    
- Database features (some DBMSs do not allow multiple SQL statements).


Okay, now we move to the practice task 9
<img width="1218" height="249" alt="image" src="https://github.com/user-attachments/assets/7622058b-3c00-4778-aedf-01e6c70ba07e" />

Here we need to retrive all users from the table using STRING SQL INJECTION
<img width="1365" height="658" alt="image" src="https://github.com/user-attachments/assets/48b850a0-d527-4d1f-ae65-faf73832f8f8" />

Lets breakdown the SQL Query
SELECT * FROM user_data WHERE first_name = 'John' and last_name = 'Smith' or '1' = '1'
we can see that '1' = '1' which is true statement thats why database ignores filtering by names and lastnames and return all available data

Lets move further to Numeric SQL Injection
<img width="1291" height="697" alt="image" src="https://github.com/user-attachments/assets/bfd59429-f534-490c-bb94-8479e459a4c8" />

Here we need to return User_id as a true statement
As you can see on the screenshot we set 1 OR '1' = '1' which always return TRUE
As a result, you get a complete list of users along with their credit card numbers
Move further to 11 task
<img width="1353" height="704" alt="image" src="https://github.com/user-attachments/assets/82305cb7-39ca-40e0-80b2-a412a4e9e671" />

Here we need to retrive all employees data
<img width="1105" height="350" alt="image" src="https://github.com/user-attachments/assets/34fb8a6a-5c38-43ee-90ae-e36685745083" />

SQL Query is simple
Smith' OR '1' = '1' --
All the data is retrived
breakdown
`OR True`
We are telling sql to return all data cuz the statement is true
Lets move to the next task
<img width="1381" height="593" alt="image" src="https://github.com/user-attachments/assets/21f398f7-d651-4ee0-8282-2b9dc173cfd7" />

As we can see our salary is $64350 and we're not happy to see those low numbers' lets cook smth here
Our SQL Query will be
Smith'; UPDATE employees SET salary = '77777777' WHERE last_name = 'Smith' --
Breakdown
after name Smith we are closing the quote ' and end the previous sql query line by adding ; to the end. After that we are able write our own sql query to lift our salary up
UPDATE - we are updating our table
SET - setting up a salary column to 77777777
WHERE - specifing our last name so the sql will understand for who to change the salary
-- is a comment line, so we are commenting all after this line
<img width="1118" height="460" alt="image" src="https://github.com/user-attachments/assets/f2ce100e-7987-426b-90c8-20bf0961f25f" />

we just updated our salary to 7777777 done
Move to the next 13's and last task
<img width="1407" height="562" alt="image" src="https://github.com/user-attachments/assets/f40071fa-171c-4c7a-8494-d89eba4263eb" />

Here we need to drop access_log table
Our SQL Query will be
Smith'; DROP TABLE access_log --
So we are simply dropping access_log table and comment everything right after it
<img width="1428" height="543" alt="image" src="https://github.com/user-attachments/assets/358797c0-5228-4894-9924-af4286b16739" />

Success, we are passed the SQL Injection Challenge
