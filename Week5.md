# Database Management Systems
* Benefits of DBMS
* Example systems

## DBMS
Open Source or free DBMS:
* SQLite
* MySQL
* Derby
* PostgreSQL

## Terminology
* Relation
    - A table
    - Consists of rows (or tuples)
* Tuple
    - A set of attribute values for a row
* Attribute
    - A table column
* Key
    - A key is an attribute or set of attributes that uniquely identifies the tuple

## Database design
* Redundancy
* Relationships
* Modelling Relationships

### Modelling relationships
* Relationships are modelled by introducting a foreign key into one of the tables.
* The foreign key stores the primary key of a row from the other table.

**One to one relationships**
* If there is a 1:1 relationship between entities in tables R and S, then either table R must contain a foreign key reference to table S, or vice versa. 

**One to many relationships**
* If there is a 1:M relationship between entities in tables R and S, then table S must contain a foreign key reference to table R.
* E.g. if a blog article has many comments, then there must be an attribute A in each comment back to the article to which it refers. Here, A is a foreign key reference to the article key. 

**Many to many relationships**
* If there is a M:N relationship between entities in tables R and S, then you must introduce a third (relationship) table with foreign key references to both tables R and S.
* This is modelled by the M:N using two 1:M relationships

## SQL
* History of SQL
* Definition files
* Creating tables
* SQL data types
* Inserting data
* Select queries
* Deleting and updating
* Naming conventions
* MySQL Command-line

### SQL History
* In web applications, an application programming interface (native, PDO, ODBC, JCDB) is used.
* SQL can be used to perform the following tasks:
    - Create, alter, and drop tables in a database.
    - Insert into, update, and delete from a table.
    - Perform select queries to retrieve information from a table.

### SQL definition files
* The SQL is saved in a definition file so that it can be run if;
    - The tables have not yet been created.
    - The table structure or initial values have changed.
    - The table contents need to be 'reset'. 
* SQL definition files end with .sql and should be placed in an sql subdirectory in the web application. 
* Usually has the following structure:
    1. Drop any existing tables if they exist.
    2. Create new tables.
    3. Insert default data (if any) into the tables.

### Creating tables
* Creating a table
```sql
    CREATE TABLE table_name(
        column1_name type constraints,
        column2_name type constraints,
        ...
    );
```
Example
```sql
    CREATE TABLE Stock(
        Id INTEGER PRIMARY KEY AUTOINCREMENT,
        Name VARCHAR(20) DEFAULT NOT NULL UNIQUE,
        Quantity INT DEFAULT '0' NOT NULL,
        Price DECIMAL (8,2) NOT NULL,
        Description TEXT
    );
```
* If the table name exists, SQL will return an error.
* In the SQL definition file you normally drop the table first to avoid the error.
```sql
    DROP TABLE IF EXISTS Stock;
    //then create table "Stock"...
```

### SQL Data types

* VARCHAR(12) - a string of at most 12 characters
* CHAR(12) - a string of 12 characters
* DECIMAL(8,2) - an 8 digit, 2 decimal places, fixed precision, floating point number (represented as a string)

### Inserting data
* The INSERT INTO statement is used to insert rows of data into tables:
```sql
    INSERT INTO table_name(column_names) VALUES(values);
```
E.g.:
```sql
    INSERT INTO Stock(Id, Name, Quantity, Price, Description) 
    VALUES 
    (NULL, "Marcel's Morsels", 1500, 1.25, "Delectable, delicious delicacies");

```

### Querying Data
* SQL queries use the SELECT statement:
SELECT values
FROM tables
WHERE conditions;

**Select ALL rows and columns from a table**
* Use SELECT * to return all columns.
* Omit the WHERE clause to return all rows, e.g.:
```sql
    SELECT *
    FROM Stock;
```

**Select SOME columns from a table**
```sql
    SELECT Name,
    Descriptiohn, Price
    FROM Stock;
```
* Specify column names after SELECT to only return those columns and in that order.

**To select one row from a table**
```sql
    SELECT *
    FROM Stock
    WHERE Id = 1;
```

**Select SOME rows from a table**
```sql
    SELECT * 
    FROM Stock
    WHERE Price = 50.00;
```

**Partial Matches**
```sql
    SELECT *
    FROM Stock
    WHERE Description LIKE '%dish%';
```
* Returns all words with 'dish'
* without the first %, it would return only words starting with 'dish'

**Return from two tables**
```sql
    SELECT first_name, last_name, course_id
    FROM Student, Enrolment
```

**Joining tables**
```sql
    SELECT first_name, last_name, course_id
    FROM Student, Enrolment
    WHERE Student.student_id = Entrolmenmt.student_id; 
```
* This indicates which table you are referring to, as you may have matching column names.

By adding extra conditions, more tables can be joined:
```sql
    SELECT first_name, last_name, name
    FROM Student, Enrolment, Course
    WHERE Student.student_id = Enrolment.student_id
    AND Enrolment.course_id = Course.course_id;
``` 

**Ordering results**
* SQL can order the results by column names in ascending or descending order 
* The order is ascending by default but can be made descending by adding DESC at the end of the ORDER BY clause
Such as:
```sql
    SELECT Name, Price
    FROM Stock
    ORDER BY Price DESC, Name;
```

**Limiting results**
* A page does not display all the results of a query usually
* SQL supports the LIMIT clause which takes two parameters, the start offset, and the maximum number of rows to return.
To return the first 30 students...
```sql
    SELECT *
    FROM Student
    LIMIT 0, 30;
```
To select the next 30 only the offset needs to be changed:
```sql
    SELECT *
    FROM Student
    LIMIT 30, 30;
```

**SQL Functions**
SQL has built-in functions that can be applied to a column to provide statistical results such as SUM, AVG, COUNT, MAX and MIN.

**Grouping results**
* We many want to apply an SQL function to a group of rows (but not the entire set).
```sql
    SELECT course_id, COUNT(student_id)
    FROM Enrolment
    GROUP BY course_id;
```

**Renaming columns**
* Columns can be renamed using the AS keyword: 
```sql
    SELECT course_id, COUNT(student_id) AS "Number of students enrolled"
    FROM Enrolment
    GROUP BY course_id;
```
So the result of COUNT(student_id) title -> Number of students enrolled

### Deleting and updating data 
**Delete all rows from a table**
```sql
    DELETE FROM Stock;
```
**Specific record**
```sql
    DELETE FROM Stock
    WHERE Id = 21;
```
**Updating data**
```sql
    UPDATE Stock
    SET Price = 9.50, Quantity = 100
    WHERE Id = 34;
```
**Multiple rows can be updated**
```sql
    UPDATE Stock
    SET Price = Price / 2
    WHERE Quantity > 1000;
```

### Naming conventions
* SQLite; table names are case insensitive

