[Home](index.md)
> Knowledge aquired with the free course in Desafio Latam platform [https://sqlinteractivo.desafiolatam.com/cursos]

# SQL Queries and Functions

## Limit and Order By
```sql
SELECT * FROM tabla WHERE campo > 10 ORDER BY campo2 LIMIT 5;
```
## Operations with Strings
### Transforming a string to uppercase/lowercase: 
Does not modify data in the table, only transforms it for the query
```sql
SELECT upper(email) as email_upper FROM users;
SELECT lower(email) as email_lower FROM users;
```

### Removing whitespace from a string
```sql
SELECT trim(name), trim(email) FROM users;
```
### Combining functions
```sql
SELECT lower(trim(name)) as clean_name, lower(trim(lastname)) as clean_lastname, lower(trim(email)) as clean_email FROM users;
```
### Getting the length of a string
```sql
SELECT length(lastname) FROM users;
SELECT lastname, length(lastname) FROM users;
```

### Getting the longest name in the table
For example, if we want to select the length of the shortest name in the users table, the query would be:
```sql
SELECT LENGTH(name) as name_length FROM users ORDER BY LENGTH(name) LIMIT 1 ;
```
On the other hand, if we want to get the length of the longest name, we will reverse the order of the selection.
```sql
SELECT LENGTH(name) as name_length FROM users ORDER BY LENGTH(name) DESC LIMIT 1 ;
SELECT length(email) FROM users ORDER BY length(email) desc LIMIT 3;
```

### Ordering all data and the function
we are asked for the longest email instead of just its length
```sql
SELECT email FROM users ORDER BY LENGTH(email) DESC LIMIT 1;
```
Additionally, we may be asked to select all fields of the user whose email is the longest. To achieve this, we can use the following query:
```sql
SELECT * FROM users ORDER BY LENGTH(email) DESC LIMIT 1;
```
Also, they may require us to select all fields of the table and additionally include the length of the email. The idea is similar, we simply add the function to the select:
```sql
SELECT *, LENGTH(email) as email_length FROM users ORDER BY LENGTH(email) DESC LIMIT 1;
SELECT email, length(email) FROM users ORDER BY length(email) desc LIMIT 3;
```

### Concatenating strings
```sql
SELECT name || ' ' || lastname AS full_name FROM employees;
SELECT product || '-' || brand as brand_product FROM products;
```

### Selecting characters FROM a string with SUBSTR
```sql
SUBSTR( string, start, length )
```
In the syntax, we can see that the function has 3 arguments:
    1. String: the name of the column or word that will be used 
    2. Start: an integer that specifies the starting position from which characters will be extracted from the string. 
    3. Length: the number of characters extracted

For example, if we have a products table with the field 'name' and want to select only the first letter of each name, we can use the following query:
```sql
SELECT SUBSTR(name, 1, 1) AS first_letter FROM products;
> to achieve the same goal in PostgreSQL, we should use the LEFT() function
SELECT substr(lastname, 1, 3) as first_letters FROM users;
SELECT substr(lastname, 2, 3) as three_characters_of_lastname FROM users
WHERE name like 'Mar%';
```

## Operations with date
### Getting today's date
DATE() we can get today's date.
In MySQL, CURDATE() is used, and in Microsoft SQL Server, GETDATE() is used. When looking for documentation, it is important to specify which engine is being used. We are using SQLite
```sql
SELECT * FROM users WHERE registration_date = DATE(); both bring information about today's date
SELECT * FROM users WHERE registration_date = DATE('now');
SELECT description FROM tasks WHERE deadline = DATE();
```
### Getting tomorrow's date
In SQL, it is possible to add dates to get future dates. In SQLite we can achieve this by passing a second argument to the DATE function
```sql
DATE('now', '1 day')
```
In this example, we are adding 1 day to today's date (now). If we want to add more days, for example, 5 days, we will use 
```sql
DATE('now', '5 day')
```
It is also possible to add weeks and months with:
```sql
2 Weeks: DATE('now', '2 week') 3 Months: DATE('now', '3 month')
```
In a query, this would look as follows:
```sql
SELECT * FROM table WHERE date > DATE('now', '2 week')
SELECT * FROM tasks WHERE deadline = DATE('now','1 day');
```
### Getting yesterday's date
Just as it is possible to add dates, it is also possible to subtract them:
```sql
DATE('now', '-1 day') DATE('now', '-1 week')
```
It is important to clarify that when we do not specify the sign, it is assumed to be positive, meaning that
```sql
DATE('now', '1 day')
> is the same as
DATE('now', '+1 day')
```
```sql
SELECT amount FROM earnings WHERE date = DATE('now','-1 day');
```
### Extracting the year
We are asked to show all the information in the table and additionally add a column with the year of the sale.
```sql
SELECT *, strftime('%Y', sale_date) as sale_year FROM sales
```
To display the results of this type of function, it is necessary to assign a name to the new column, otherwise, the resulting column will retain the name "strftime('%Y', sale_d[...].
```sql
SELECT amount, strftime('%Y',sale_date) as sale_year FROM sales;
```
### Extracting the month
```sql
SELECT strftime('%m', column) FROM table
```
In this case, to get the month, we pass %m as an argument to the strftime function.
```sql
SELECT amount, strftime('%m',sale_date) as sale_month, strftime('%Y',sale_date) as sale_year FROM sales;
```
### Extracting both the month and year
We have learned to extract the month and the year from a date. However, how could we extract both data in a single column?
To extract both the month and the year from a date in a single column, you can use the function strftime('%m-%Y'). This will allow you to get a result in the format "month-year".
```sql
SELECT amount, strftime('%m-%Y',sale_date) as month_year FROM sales;
```
### Extractions and Where
We are asked to show all sales from the year 2012. For this, we will use the strftime function to extract the year from the dates, and then filter by the indicated year:
```sql
SELECT * FROM sales WHERE strftime('%Y', sale_date) = '2012';
SELECT * FROM sales WHERE strftime('%Y',sale_date) = '2015';
```

## Aggregation functions
### The highest value in a column
the function MAX() which allows us to find the highest value in the specified field.
```sql
SELECT MAX(column) FROM table
```
We can find the highest salary using:
```sql
SELECT MAX(salary) FROM employees;
```
When we use aggregation functions, we cannot directly select other items from the same table. 
For example, 
```sql
SELECT email, MAX(salario) FROM empleados; would throw an error because we would be selecting email along with the function.
SELECT MAX(age) FROM employees;
```
### The lowest value in a column
This function takes as an argument the name of the column and returns the smallest value in that column.
```sql
SELECT MIN(column) FROM table
SELECT MIN(salary) FROM employees;
```
### Sum of elements in a column
SUM(). With this, we can sum all the elements of a column.
SELECT SUM(column) FROM table
It is important to note that the column on which the SUM() function is applied must contain numerical values, otherwise, the query may generate an error or an unexpected result.
```sql
SELECT sum(salary) FROM employees;
```
### Average of a column
AVG(). The name of the function comes FROM the English term average
SELECT AVG(column) FROM table
```sql
SELECT AVG(salary) FROM employees;
```
### Counting elements in a table
COUNT(). With this, we can count the number of records in a table.
SELECT COUNT(*) FROM table
```sql
SELECT count(*) FROM employees;
```
--Exercise 1: Aggregation functions with Where
```sql
SELECT AVG(column1) FROM table WHERE column2 < value
SELECT sum(salary) FROM employees WHERE age > '27'
```
--Exercise 2: Aggregation functions with WHERE
```sql
SELECT AVG(salary) FROM employees WHERE salary > '50000'
```
--Exercise 3
```sql
SELECT count(*) FROM employees WHERE department = 'Marketing'
```
--Exercise 4:
And is exclusive. when you want to filter by multiple values in a column, you must use OR instead of AND
```sql
SELECT count(*) FROM employees 
WHERE department = 'Finance'
or department = 'Marketing';
```
### Counting with string conditions
```sql
SELECT count(*) FROM users WHERE name like'%a'
```

## Distinct
### Selecting and filtering repeated data
In SQL, the DISTINCT keyword allows us to filter repeated results from a query.
```sql
SELECT DISTINCT color AS unique_color
FROM colors
SELECT distinct color as unique_color FROM colors;
```
### Selecting unique emails
```sql
SELECT distinct email as unique_email FROM users;
```
### Selecting distinct years
We are asked to create a query that shows the years in which transactions have been made, excluding repetitions.
As we learned in previous exercises, to get the year from the sale date, we can use the following code:
```sql
SELECT strftime('%Y', sale_date) as sale_year FROM sales
```
However, to ensure we obtain unique years, we can add the DISTINCT clause to our query as follows:
```sql
SELECT DISTINCT strftime('%Y', sale_date) as unique_year FROM sales
SELECT distinct strftime('%m', sale_date) as unique_month FROM sales;
```
### Counting distinct values
If we want to count the distinct values in a column of a table, we can combine the COUNT and DISTINCT functions as follows:COUNT(DISTINCT column)
```sql
SELECT count(distinct phone) as unique_phones FROM users;
```
### Counting unique emails
```sql
SELECT count(distinct email) as email_count FROM users;
```
### Distinct with multiple columns
We can use DISTINCT with more than one column to obtain unique combinations of those columns. Suppose you have a table called employees with the columns department and position.
We can then get all the unique combinations of Department and Position using the following query:
```sql
SELECT DISTINCT department, position FROM employees;
SELECT distinct category, price FROM products;
```
## Introduction to Groups
### Grouping values with GROUP BY
The GROUP BY clause is a powerful tool in SQL that is used to group rows with identical values in one or more specific columns, allowing for aggregation operations[...]
In this first exercise, we will learn to use GROUP BY to get all the distinct elements of a table, the same as we previously did with distinct.
We can select unique elements using GROUP BY as follows:
```sql
SELECT color as unique_color FROM colors GROUP BY color
SELECT email as unique_email FROM users group by email;
```
### Grouping and counting
GROUP BY is commonly used together with aggregation functions such as COUNT, MAX, MIN, SUM, and AVG to obtain summarized information FROM a dataset.
We want to know how many times each color appears. We can achieve this by combining GROUP BY and the aggregation function COUNT
```sql
SELECT color, COUNT(color) as Repetitions FROM colors GROUP BY color
SELECT email, count(email) as repetitions FROM users group by email;
```
--Practicing grouping and counting
```sql
SELECT department, count(department) as employee_count FROM employees group by department;
```
### Grouping and summing
If we want to calculate how much each customer has spent, we can perform the following query
```sql
SELECT Customer, SUM(Amount) AS Total_Amount FROM orders GROUP BY Customer;
SELECT category, SUM(amount) as total_amount FROM sales group by category;
```
### Grouping and averaging
calculating averages by group.
```sql
SELECT group, AVG(column) FROM table GROUP by group
SELECT full_name, AVG(grade) as Grade_Average FROM students group by full_name;
```
### Maximum by group
getting the highest amount in each group. The syntax of the query will be the same as seen previously, i.e.:
```sql
SELECT group, MAX(column) FROM table GROUP by group
SELECT category, MAX(amount) as highest_amount FROM sales group by category;
```
### Minimum by group
getting the lowest amount in each group. The syntax of the query will be the same as seen previously, i.e.:
```sql
SELECT group, MIN(column) FROM table GROUP by group
SELECT category, MIN(amount) as lowest_amount FROM sales group by category;
```
### Aggregation functions and dates
When building reports, we frequently need to deliver information grouped by a period of time. To achieve this, we will use a combination of GROUP BY with the strftim[...]
We are asked to determine the total amount of sales per year. To solve this, we have to group by date and sum the amounts as follows:
```sql
SELECT SUM(amount), strftime("%Y", sale_date) AS year FROM sales GROUP BY strftime("%Y", sale_date)
SELECT sum(amount) as sale_sum, strftime('%m', sale_date) as month FROM sales group by strftime('%m', sale_date);
SELECT strftime('%m',registration_date) as month, count(registration_date) as user_count FROM registrations group by strftime('%m', registration_date);
```
### Grouping without specifying column names
When it comes to grouping data in an SQL query, there is a way to avoid the redundancy of the select clause. For example, consider the following query:
```sql
SELECT strftime("%Y", sale_date) AS year, SUM(amount) FROM sales GROUP BY strftime("%Y", sale_date)
> You can simplify it as follows:
SELECT strftime("%Y", sale_date) AS year, SUM(amount) FROM sales GROUP BY 1
> This notation is interpreted as "group by the first criterion". It is also possible to apply this syntax in the ORDER BY clause:
SELECT strftime("%Y", sale_date) AS year, SUM(amount) FROM sales GROUP BY 1 ORDER BY 1
> This way, you can achieve the same grouping and ordering without repeating the select clause expression.
SELECT email, count(email) as repetitions FROM users group by 1 ORDER BY 1;
```
### Grouping by multiple columns
In SQL, it is possible to group by multiple columns using the following syntax:
```sql
SELECT column1, column2, aggregation_function(column3) FROM table GROUP BY column1, column2
> And as we learned in the previous exercise, we can also write the query as follows:
SELECT column1, column2, aggregation_function(column3) FROM table GROUP BY 1, 2
```
Calculate the average of each student in each subject. The columns should be named email, subject, and grade_average
```sql
SELECT email, subject, avg(grade) as grade_average FROM students group by 1,2;
```
## Having
### Introduction to Having
In SQL, the GROUP BY clause allows us to group data. If we want to filter the obtained information, we will use HAVING.
HAVING is used to filter the results of a query that involves aggregated functions. In other words, HAVING allows applying filtering conditions to the results of functions like COUNT[...]
We are asked to create a report showing the months and the number of enrollees, but only where there are 2 or more enrollees.
```sql
SELECT strftime("%m", Registration_Date) AS month, COUNT(Registration_Date) user_count 
FROM registrations 
GROUP BY strftime("%m", Registration_Date)
HAVING user_count >= 2
```
In this query, we first use GROUP BY to group by month. Then, we use the aggregation function COUNT(Registration_Date) to count the number of enrollees. After grouping[...]
```sql
SELECT strftime('%m', registration_date) as month, count(registration_date) as user_count FROM registrations
group by strftime('%m', registration_date)
having user_count = 1;
```

### Finding duplicates
One of the most common uses of HAVING is finding duplicates.
Show the emails that appear more than once. The result table should have two columns: one named email, and another named email_count that shows the number of repetitions correspond[...]
```sql
SELECT email, count(email) as email_count FROM corporate_emails
group by email
having email_count > 1;
```

### Having and count
Create a query that shows the number of users and the department where there are more than one employee. The columns should be named user_count and department, respectively.
```sql
SELECT count(name) as user_count, department FROM employees
group by department
having user_count > 1;
```

### Having and average
Create a query to determine which students passed. The passing criterion is an average grade >= 50.
The columns to show should be email and grade_average.
```sql
SELECT email, avg(grades) as grade_average FROM grades
group by email
having grade_average >= 50;
```

### Having and order
Once we have grouped data using the GROUP BY clause, it is common to need to order those groups according to some specific criterion. Generally, we want to order the groups by func[...]
The order of the clauses in a query should be as follows:
| Order | Clause | Description |
| ----- | ------ | ----------- |
| 1 | SELECT | Specifies the columns to be returned in the result. |
| 2 | FROM | Specifies the tables from which to extract the data. |
| 3 | WHERE | Filters records before any aggregation or grouping. |
| 4 | GROUP BY | Groups records by one or more columns. |
| 5 | HAVING | Filters records after aggregation. |
| 6 | ORDER BY | Orders the returned records by one or more columns. |
| 7 | LIMIT | LIMITs the number of returned records. |

Given the following sales table, write an SQL query to obtain the products that have been sold in a total quantity greater than 1000, ordered in descending order of quantity sold.
```sql
SELECT product, sum(quantity) as total_quantity FROM sales
group by product
having total_quantity > 1000 ORDER BY 2 desc;
```
Your task is to write an SQL query that returns the departments whose average salary is greater than 3000, ordered FROM highest to lowest average salary. The results should show the department name[...]
```sql
SELECT Department, AVG(salary) as Average_Salary FROM employees
group by Department
having Average_Salary > 3000 ORDER BY 2 desc;
```

## Subqueries
### Introduction to subqueries
Subqueries, allow us to use the results of one query within another query.
We are asked to select all people earning above the average.
These types of questions can be answered using subqueries.
The idea to answer this is as follows:
We calculate the average 
```sql
SELECT avg(salary) FROM employees
```
We select all employees whose salary is greater than the previous query. 
```sql
SELECT * FROM employees WHERE salary > (SELECT AVG(salary) FROM employees)
```
```sql
SELECT * FROM employees WHERE salary <= (SELECT avg(salary) FROM employees)
```

### Subqueries and WHERE part 1
Within subqueries, we can use the same clauses we have learned so far, such as the where clause. This means we can apply the where clause both inside the sub[...]

Select all the information of the records that are greater than the average salary of the finance department.
```sql
SELECT * FROM employees WHERE salary > (SELECT avg(salary) FROM employees WHERE department = 'Finance')
```
Using the data FROM the employees table, select all employees whose salary is higher than the employee with the highest salary in the finance department.
```sql
SELECT * FROM employees WHERE salary > (SELECT max(salary) FROM employees WHERE Department = 'Finance')
```
Select all records above the average grade.
```sql
SELECT * FROM grades WHERE grades > (SELECT avg(grades) FROM grades)
```
### Subqueries with IN
The IN operator is a very useful operator in subqueries
We want to select all the codes from Argentina, Brazil, Chile, or Colombia.
One way to approach the problem would be to combine all options with where and multiple OR operators.
Another option is using the IN operator as follows:
```sql
SELECT * FROM paises WHERE pais IN ('Argentina', 'Brasil', 'Chile', 'Colombia');
```
Similarly, we can make a query like the following:
```sql
SELECT * FROM table WHERE columna IN (SELECT * FROM otra_tabla);
```
We are asked to show the names of all the people who have an average grade less than 50.
Select the IDs from the "notas" table with promedio_notas <= 50
Select the names from the "estudiantes" table whose ID is within the previous subquery.
```sql
SELECT nombre FROM estudiantes WHERE estudiante_id IN (SELECT estudiante_id FROM notas WHERE promedio_notas <= 50);
```
SELECT the names FROM the "estudiantes" table whose ID is within the subquery:
```sql
SELECT nombre FROM estudiantes WHERE estudiante_id IN (SELECT estudiante_id FROM promedios WHERE promedio_notas > 50);
```
Create a query that shows all titles with valoracion_promedio > 4.
The resulting column should be called nombres_seleccionados.
```sql
SELECT nombre AS nombres_seleccionados FROM libros WHERE libro_id IN (SELECT libro_id FROM valoraciones WHERE valoracion_promedio > 4);
```
We are asked to obtain the names of all patients who had their last consultation before May 16, 2023.
The column should be called nombres_pacientes.
```sql
SELECT nombre AS nombres_pacientes FROM pacientes WHERE paciente_id IN (SELECT paciente_id FROM consultas WHERE fecha_consulta < '2023-05-16');
```
### Subqueries in the from clause
A subquery in the from clause has the following form.
```sql
SELECT * FROM (SELECT * FROM tabla1);
```
We have the "ventas" table that has the seller's code and the amount of each sale made.
We are asked to know the total average sold.
To do this, we first need to sum the amounts per seller and then take the average of sales over these results.
```sql
SELECT AVG(total_venta) AS promedio_ventas FROM (SELECT empleado_id, SUM(monto) AS total_venta FROM ventas GROUP BY empleado_id);
```
We have the "goles" table that records the goals scored by each player in different matches.
We are asked for a query to calculate the total average goals per player.
> [!TIP] 
> It's not the same as the average goals per match.
```sql
SELECT AVG(total_goles) AS promedio_goles FROM (SELECT jugador_id, SUM(goles) AS total_goles FROM goles GROUP BY 1);
```
## Combining queries
### Introduction to the SQL UNION clause
The UNION operator in SQL is used to combine the result of two or more select statements into a single result set.
The basic syntax of UNION is as follows:
```sql
SELECT columna1, columna2 FROM tabla1 UNION SELECT columna1, columna2 FROM tabla2;
```
The columns selected in the SELECT statements must have the same column names, sequence, and data types.
```sql
SELECT apellido FROM Estudiantes UNION SELECT apellido FROM Profesores;
```
SELECT names FROM "estudiantes" and combine with names FROM "profesores":
```sql
SELECT nombre AS nombres FROM estudiantes UNION SELECT nombre FROM profesores;
```
### Removing duplicates with UNION
The main feature of UNION is that it removes duplicate rows FROM the final result.
```sql
SELECT email AS correos_unicos FROM usuarios UNION SELECT email FROM clientes;
```
### UNION vs UNION ALL
In the previous exercises, we learned that the UNION operator is used to combine the results of two or more select statements into a single result set, removing duplicate rows.
If we want to get the duplicate rows in the result, we use the UNION ALL operator.
We can combine both tables using UNION ALL as follows:
```sql
SELECT * FROM tabla1 UNION ALL SELECT * FROM tabla2;
```
Exercise: Given the following tables empleados1 and empleados2
empleados1:
| Nombre | Apellido | Edad |
| ------ | -------- | ---- |
| Juan | Pérez | 30 |
| María | González | 25 |
| Carlos | Rodríguez | 40 |

empleados2:
| Nombre | Apellido | Edad |
| ------ | -------- | ---- |
| Ana | Martínez | 22 |
| María | González | 25 |
| Carmen | López | 25 |

Create a query that combines both tables including the duplicate rows.
```sql
SELECT * FROM empleados1 UNION ALL SELECT * FROM empleados2;
```
### Introduction to INTERSECT
The INTERSECT operator is used to combine two select statements and return the results that are found in both queries.
We can find the common clients using INTERSECT as follows:
```sql
SELECT nombre FROM clientes1 INTERSECT SELECT nombre FROM clientes2;
```
SELECT common elements FROM "lista1" and "lista2":
```sql
SELECT * FROM lista1 INTERSECT SELECT * FROM lista2;
```
### The EXCEPT operator
The EXCEPT operator in SQL is used to return all rows in the first query that are not present in the second query.
In other words, EXCEPT returns only the rows that are part of the first query but not the second query.
We can use EXCEPT to find the names that are in 'Tabla1' but not in 'Tabla2' with the following query:
```sql
SELECT nombre FROM Tabla1 EXCEPT SELECT nombre FROM Tabla2;
```
Create a query that shows the names of the employees who are not managers.
```sql
SELECT nombre FROM empleados EXCEPT SELECT nombre FROM gerentes;
```
## Inserting Records
### Adding a record to a table
With SQL we can insert new data into existing tables.
To achieve this, we use the INSERT statement.
We accompany the INSERT statement with the INTO keyword to specify which table we want to insert a value into and VALUES to specify the values we want to insert.
For example, if we have a table called "productos" with the columns id, nombre, and precio, we can add a new product to the table using:
```sql
INSERT INTO productos VALUES (1, 'Camiseta', 2000);
```
For each column in the table, we must enter the corresponding values in the same order as defined in the statement.
We must use single quotes for text data type values.
```sql
INSERT INTO usuarios VALUES (7, 'Lucía', 'Sánchez', 'luciasanchez@outlook.com', '555-5555');
INSERT INTO productos VALUES (7, 'Bolso', 1000, 10);
```
### Specifying null values
When inserting data, if there is a value we do not know or do not want to specify, we can enter a null value.
```sql
INSERT INTO productos VALUES (1, 'Camiseta', NULL, NULL);
```
Insert into "productos" with some NULL values:
```sql
INSERT INTO productos VALUES (7, 'Bolso', 1000, NULL);
```
### Adding a record specifying columns
When inserting data, it is possible to specifically mention the columns to be inserted, instead of mentioning all values in the order defined in the table.
We are asked to insert a new product with the following data, but specifying the columns:
| id | nombre | precio | stock |
| ------ | -------- | ---- | ---- |
| 7 | Bolso | 1000 | 10 |

```sql
INSERT INTO productos (id, precio, nombre, stock) VALUES (7, 1000, 'Bolso', 10);
```
One advantage of this method is that it is not necessary to enter the values in the same order as defined in the table.
```sql
INSERT INTO usuarios (id, apellido, nombre, telefono, email) VALUES (7, 'Sánchez', 'Lucía', '333-3333', 'luciasanchez@outlook.com');
```
### Adding a record specifying only some columns
Another benefit of specifying columns when inserting data is that null values will automatically be inserted into the columns not mentioned.
We can enter the product "Gorro" with a price of 1000 and leave the stock null as follows:
```sql
INSERT INTO productos (nombre, precio) VALUES ('Gorro', 1000);
```
Insert into "productos" with some columns specified:
```sql
INSERT INTO productos (nombre, stock) VALUES ('Bolso', 10);
```
### Adding today's date to a record
If we want to insert the current date when creating a record, we can use the CURRENT_DATE function to obtain it.
Example:
```sql
INSERT INTO usuarios (nombre, fecha_creacion) VALUES ('Gonzalo', CURRENT_DATE);
```
Insert into "productos" with current date:
```sql
INSERT INTO productos (nombre, stock, fecha) VALUES ('Bolso', 10, CURRENT_DATE);
```
### Adding date and time when inserting
If we want to insert any date when creating a record, we simply need to specify the date in the expected format.
The date format is: YYYY-MM-DD, i.e., year-month-day, where the year is 4 digits, the month is 2 digits, and the day is 2 digits.
Example:
```sql
INSERT INTO usuarios (nombre, fecha_creacion) VALUES ('Gonzalo', '2021-01-01');
```
Insert a new product with the following data:
nombre: Bolso
stock: 10
fecha: fecha_con_formato
The product's date should be January 1, 2023.
```sql
INSERT INTO productos (nombre, stock, fecha) VALUES ('Bolso', 10, '2023-01-01')
```
### Adding multiple values
We can insert multiple records into a table in a single INSERT statement.
To do this, we need to specify the values for each record separated by commas.
For example, if we have a table called 'ventas' with the columns 'producto', 'cantidad', and 'precio',
we can add multiple records to the table using:
```sql
INSERT INTO ventas VALUES ('Camiseta', 5, 2000), ('Pantalón', 3, 1500), ('Zapatos', 2, 3000);
```
Insert multiple values into the 'ventas' table:
```sql
INSERT INTO ventas VALUES ('Gorro', 5, 1000), ('Camiseta', 10, 500), ('Pantalón', 8, 1500);
```
### Creating a record with an autoincrement field
In an SQL database, it is possible to streamline the process of inserting data into a table by using an autoincrement field.
This type of field is especially useful for managing unique identifiers, such as the 'id' field of a table.
The autoincrement feature is achieved by using the AUTOINCREMENT clause in the field definition.

To illustrate this process, let's consider a table called 'empleados' with three columns: 'id' (autoincrement), 'nombre', and 'apellido'.
This is how the table is created:
```sql
CREATE TABLE empleados (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    nombre TEXT,
    apellido TEXT
);
```
Here, we have defined the 'id' column as an autoincrement field using the AUTOINCREMENT clause,
which ensures that a unique and incrementing value will be automatically generated for each new record.
Suppose we want to insert a new employee into this table. We can use the following SQL query:
```sql
INSERT INTO empleados (nombre, apellido) VALUES ('John', 'Doe');
```
When executing this query, a new employee will be created in the 'empleados' table.
The 'id' column will be automatically incremented, while the provided values for 'nombre' and 'apellido' will be stored in the corresponding columns.
This ensures that each new employee will have a unique identifier and that the insertion process is more efficient.

Given the 'empleados' table with the columns 'id', 'nombre', and 'apellido', create a new employee with the name "Jane" and the surname "Smith".
```sql
INSERT INTO empleados (nombre, apellido) VALUES ('Jane', 'Smith');
```
### Adding a record with a default value
When creating a table in SQL, you can assign default values to its columns.
This means that when inserting new data, if no specific value is provided for a column, the default value will be automatically used.
Suppose we want to create a table called 'Productos' with the following columns:
ID (unique identifier for the product)
Nombre (product name)
Precio (product price, with a default value of 10)
```sql
CREATE TABLE Productos (
    ID INTEGER PRIMARY KEY AUTOINCREMENT,
    Nombre TEXT,
    Precio INTEGER DEFAULT 10
);
```
If we insert a new product with only the name, the default price value will be automatically used:
```sql
INSERT INTO Productos (Nombre) VALUES ('Ejemplo Producto');
```
In this case, the product will be inserted with a value of 10 in the 'Precio' column.
If we want to insert a product with a different price, we simply provide the corresponding value:
```sql
INSERT INTO Productos (Nombre, Precio) VALUES ('Otro Producto', 25);
```
Given the 'usuarios' table with the columns 'id', 'nombre', 'apellido', 'email', and 'telefono', create a new user with the following values:
nombre: Lucía
apellido: Sánchez
email: luciasanchez@outlook.com
The 'telefono' column will have the default value 111-1111
```sql
INSERT INTO usuarios (nombre, apellido, email) VALUES ('Lucía', 'Sánchez', 'luciasanchez@outlook.com');
```
## Deleting and modifying records
### Deleting all records from a table
In SQL, the delete clause is used to delete records from a table.
When the delete from table_name statement is executed, all records in the specified table are deleted.
It is important to note that this operation is irreversible and will permanently delete the data from the table, so you must be very careful when using this statement.
```sql
DELETE FROM productos;
> [!NOTE]
> the delete statement does not include the *
```
### Deleting a specific record with Where
The delete statement is used to delete data from a table.
If we want to delete specific rows instead of all the data in the table, we can use the where clause along with the delete statement.
This allows us to specify a condition to determine which rows will be deleted.
For example, if we have a table of products and want to delete only those products with a price equal to 1000, we can use the following query:
```sql
DELETE FROM productos WHERE precio = 1000;
```
Delete a specific record in the 'usuarios' table:
```sql
DELETE FROM usuarios WHERE id = 2;
```
### Editing records
The update statement is used to modify existing data in a table.
It is used as follows:
```sql
UPDATE table_name SET column_name = new_value
```
Suppose we have a 'ventas' table with a column called 'total'.
If we want to increase the total of all sales recorded in the table by 10%, we can do it as follows:
```sql
UPDATE ventas SET total = total * 1.10;
```
Edit the 'registrado' column so that all users have the value TRUE:
```sql
UPDATE usuarios SET registrado = TRUE;
```
### Editing specific records using Where
If we want to edit only some rows in our table, we can use update in conjunction with where.
In this way, only the records that meet the specified condition will be modified.
```sql
UPDATE table_name SET column_name = new_value WHERE condition;
```
Suppose we manage a table called 'empleados' that contains information about the employees of a company.
Among the columns are id_empleado, nombre, salario, and departamento.
If we want to increase the salary by 15% only for employees who work in the 'Sales' department, we can use the update statement along with where as follows:
```sql
UPDATE empleados SET salario = salario * 1.15 WHERE departamento = 'Ventas';
```
Edit the phone number of the user with ID 4:
```sql
UPDATE usuarios SET telefono = '123-456' WHERE id = 4;
```
### Editing multiple columns
In SQL, it is possible to edit multiple columns of a record using the SET clause.
To do this, we need to specify the name of each column we want to modify, followed by the new value we want to assign to it.
```sql
UPDATE table_name SET column1 = 'new_value', column2 = 'new_value', column3 = 'new_value' WHERE condition;
```
Exercise: We have a 'posts' table with the following columns:
| Column | Data Type |
| ------ | -------- |
| id | INTEGER |
| titulo | TEXT |
| contenido | TEXT |
| autor | TEXT |
| fecha | TEXT |

Edit the post with id 1 to have the title "Aprendiendo SQL" and the content "SQL is a programming language for managing relational databases".
```sql
UPDATE posts
SET 
    titulo = 'Aprendiendo SQL',
    contenido = 'SQL es un lenguaje de programación para gestionar bases de datos relacionales'
WHERE id = 1;
```
## Tables
### Our first table
To create a table in SQL, the CREATE TABLE statement is used as follows:
CREATE TABLE table_name (column_name data_type)
This statement allows defining the structure of the table, including the column names and their data types.
Let's see an example of how to create a 'productos' table that includes different data types for the columns:
```sql
CREATE TABLE productos (nombre TEXT);
```
Once the table is created, we can insert data as we learned in previous exercises:
```sql
INSERT INTO productos VALUES ('Ipad Pro 2022'), ('Iphone 13 Pro Max'), ('Macbook Pro 2023');
```
Exercise: Create a table called 'alumnos' that stores a column 'nombre' of type text
| Column | Data Type |
| ------ | -------- |
| nombre | text |

Insert a record into the created table using the following data:
nombre: Lucía
```sql
CREATE TABLE alumnos (nombre text);
INSERT INTO alumnos VALUES ('Lucía');
```
### A table with multiple columns
When creating a table, we can specify multiple columns, each with its name and data type.
For example, if we want to create a 'productos' table that includes the name, description, and price of each product, we can do it as follows:
```sql
CREATE TABLE productos (nombre TEXT, descripcion TEXT, precio INT);
```
Exercise: Create a table called 'alumnos' with the following columns:
| Column | Data Type |
| ------ | -------- |
| nombre | text |
| apellido | text |
| telefono | text |

Insert a record into the created table using the following data:
nombre: Lucía
apellido: Sánchez
telefono: 12345678
```sql
CREATE TABLE alumnos (nombre text, apellido text, telefono text);
INSERT INTO alumnos VALUES ('Lucía', 'Sánchez', '12345678');
```
### Tables with different data types
In addition to text data, we can use other data types. In this exercise, we will cover the following three types:
INTEGER to store integers
BOOLEAN to store true or false values
DATE to store dates

Create a table called 'usuarios' with the following columns:
| Column | Data Type |
| ------ | -------- |
| nombre | text |
| apellido | text |
| edad | integer |
| activo | boolean |
| nacimiento | date |
  
Then insert a record into the created table using the following data:
nombre: Lucía
apellido: Sánchez
edad: 25
activo: true
nacimiento: 1996-01-01
```sql
CREATE TABLE usuarios (nombre text, apellido text, edad integer, activo boolean, nacimiento date);
INSERT INTO usuarios VALUES ('Lucía', 'Sánchez', 25, true, '1996-01-01');
```
### Real data types
In this exercise, we will see the REAL data type, which allows storing numbers with decimals.

Exercise: Create a table called 'temperaturas' with the column 'temperatura_celsius':
Column              Data Type
temperatura_celsius real
Then insert the following records:
temperatura_celsius
23.4
26.5
27.1
> [!IMPORTANT]
> To enter the decimal part of the numbers, use the dot (.) as the decimal separator
```sql
CREATE TABLE temperaturas (temperatura_celsius double);
INSERT INTO temperaturas VALUES (23.4), (26.5), (27.1);
```
### Deleting a table
In SQL, we can use the DROP TABLE command to delete a table.
For example, if we want to delete the 'temperaturas' table that we created in the previous exercise, we can do it with the following query:
```sql
DROP TABLE temperaturas;
```
If we try to select from the 'temperaturas' table after deleting it, we will get an error.

Exercise: In this exercise, we will have a table with data that we do not need.
We will delete it, create it again, and populate it with the requested data.
Delete the 'temperaturas' table and recreate it with the following columns:
| Column | Data Type |
| ------ | -------- |
| ciudad | text |
| temperatura | REAL |
| fecha | Date |

Then insert the following records:
ciudad       temperatura fecha
Buenos Aires 20.0        2024-01-01
Buenos Aires 21.0        2024-01-02
Santiago     22.0        2024-01-01
Santiago     23.0        2024-01-02
```sql
DROP TABLE temperaturas;
CREATE TABLE temperaturas (ciudad text, temperatura double, fecha date);
INSERT INTO temperaturas VALUES 
('Buenos Aires', 20.0, '2024-01-01'), 
('Buenos Aires', 21.0, '2024-01-02'), 
('Santiago', 22.0, '2024-01-01'), 
('Santiago', 23.0, '2024-01-02');
```
### Updating a table
In this exercise, we will learn how to add a column to an existing table.
To do this, we will use the ALTER TABLE statement, which allows us to modify the definition of a table.
The syntax for doing this is as follows:
```sql
ALTER TABLE table_name ADD COLUMN column_name data_type;
```
Where we need to specify the name of the existing table, the name of the new column, and the data type we will use.
For example, if we have a 'personas' table with the columns 'nombre' and 'apellido', and we want to add the 'edad' column of type INTEGER, we can do it as follows:
```sql
ALTER TABLE personas ADD COLUMN edad INTEGER;
```
Exercise: In this exercise, we will modify the 'productos' table to add the 'descripcion' column of type TEXT.
Currently, the 'productos' table has the following columns:
| Column | Data Type |
| ------ | -------- |
| nombre | text |
| precio | real |

After creating it, you must insert the following records:
| Nombre | Precio | Descripcion |
| ------ | ------ | -------- |
| Camisa | 1000.00 | Camisa de manga corta |
| Pantalón | 2000.00 | Pantalón de mezclilla |
| Camisa XL | 1000.00 | Camisa de manga larga |
```sql
ALTER TABLE productos ADD COLUMN descripcion text;
INSERT INTO productos VALUES 
('Camisa', 1000.00, 'Camisa de manga corta'),
('Pantalón', 2000.00, 'Pantalón de mezclilla'),
('Camisa XL', 1000.00, 'Camisa de manga larga');
```
## Restrictions - Constraints
### Introduction to restrictions
Key ideas
Restrictions or constraints are rules that can be applied to the columns of a table.
The NOT NULL restriction is a type of restriction that prevents null values from being entered into a column.
If we enter a null value in a column with the NOT NULL restriction, the operation will fail.

Adding a restriction when creating a table

When creating tables, we can add restrictions (constraints) to the columns to prevent data that does not meet certain conditions from being entered.
In this exercise, we will learn to add the NOT NULL restriction, which prevents null values from being added to a column.
For example, when creating a table of people with name and surname, we can make the name mandatory (not null) and the surname optional.
To achieve this, we will create the table and add the NOT NULL restriction to the name column as follows:
```sql
CREATE TABLE personas (
    nombre TEXT NOT NULL,
    apellido TEXT
);
```
To add a restriction, we simply need to specify it along with the column.
create table empleados (nombre text not null, apellido text);
```sql
INSERT INTO empleados VALUES ('Pedro', 'Pérez');
```
### Adding a restriction to an existing table
Key ideas
A restriction can be added when creating a table.
In SQLite, restrictions cannot be directly added to already created tables.
However, we can achieve this by creating a new table with the restriction, copying the data from the original table to the new table, deleting the original table, and renaming the new table to the original table name.

For example, if we have a table 'personas' with the columns 'nombre' and 'apellido'.
```sql
Column	Type	Restrictions
nombre	TEXT
apellido	TEXT
and we want to add the NOT NULL restriction to the 'nombre' column.
What we need to do is:
Create a new table with the restriction.
/* 1. Create the new table with the restriction */
CREATE TABLE personas2 (
    nombre TEXT NOT NULL,
    apellido TEXT
);

Copy the data from the original table to the new table.
/* 2. Copy the data from the original table to the new table */
INSERT INTO personas2 (nombre, apellido)
SELECT nombre, apellido
FROM personas;

Delete the original table.
/* 3. Delete the original table */
DROP TABLE personas;

Rename the new table to the original table name.
/* 4. Rename the new table to the original table name */
ALTER TABLE personas2 RENAME TO personas;

In other database engines like PostgreSQL or MySQL, it is possible to add restrictions to existing tables without creating a new table.
```
Exercise
```sql
create table patentes2 (patente text not null);
INSERT INTO patentes2 (patente)
SELECT patente FROM patentes;
DROP TABLE patentes;
ALTER TABLE patentes2 RENAME TO patentes;
```
### Removing a restriction
Just as restrictions can be added to columns, they can also be removed.
Removing a restriction from an existing table
To remove a restriction from an existing table in SQLite, we need to follow a procedure similar to adding a restriction.
1. Create a new table without the restriction.
2. Copy the data from the original table to the new table.
3. Delete the original table.
4. Rename the new table to the original table name.

Exercise
```sql
CREATE TABLE personas2 (nombre text, apellido text, edad integer);
INSERT INTO personas2 (nombre, apellido, edad)
SELECT nombre, apellido, edad
FROM personas;
DROP TABLE personas;
ALTER TABLE personas2 RENAME TO personas;
```
### Unique constraint
Key ideas
There are different types of restrictions that can be applied to the columns of a table.
The uniqueness constraint, or Unique, prevents duplicates in a specific column.
A very popular case of this restriction is to avoid people with the same email address.

To add a Unique constraint, we simply need to specify it when creating the table.
For example, if we want the email address of people to be unique, we can create the table as follows:
```sql
CREATE TABLE personas (
    nombre text,
    apellido text,
    email text UNIQUE
);

Create a table with Unique constraint
create table productos (nombre text not null, precio double not null, codigo text unique);
INSERT INTO productos VALUES
('Camisa', 1000.00, 'CAM-001'),
('Pantalón', 2000.00, 'PAN-001'),
('Camisa XL', 1000.00, 'CAM-002');
```
### Check constraints
Key ideas
1. There are different types of restrictions that can be applied to the columns of a table.
2. The NOT NULL restriction prevents null values from being entered into a column.
3. The UNIQUE restriction prevents duplicates in a specific column.
4. The CHECK restriction allows us to establish a condition that the values of a column must meet.

Check constraints

The CHECK restriction allows us to establish a condition that the values of a column must meet.
For example, if we want the salary of employees to be greater than zero, we can add a CHECK restriction to the 'salario' column.
To add a CHECK restriction, we simply need to specify it in the column definition, providing the condition that the column value must meet.
For example:
```sql
CREATE TABLE empleados (
    nombre TEXT,
    salario REAL CHECK (salario > 0)
);
```
### Create a table with CHECK constraint
create table productos (nombre text not null, precio double not null, stock integer check(stock >= 0));
```sql
INSERT INTO productos VALUES
('Camisa', 1000.00, 10),
('Pantalón', 2000.00, 5),
('Camisa XL', 1000.00, 3);
```
### Primary key
There are different types of restrictions that can be applied to the columns of a table, such as NOT NULL, UNIQUE, and CHECK.
The Primary Key restriction prevents null values from being entered and ensures that there are no duplicates in a specific column.
Practically, we can say that it is a combination of Unique and Not Null.
**What is a primary key?**
The primary key, or PRIMARY KEY in English, is a restriction that serves to uniquely identify each record in a table.
For example, suppose we have a table called 'boletas' with the following records:
| id | monto de la boleta | fecha de emision |
| ---- | ---------------- | ---------------- |
| 1 | 10.000 | 2021-10-01 |
| 2 | 12.000 | 2021-10-02 |
| 3 | 16.000 | 2021-10-03 |

And we want to find the ticket with id 2.
* If we did not have a primary key, we could have two tickets with the same id, and we would not know which one we want, or we could have tickets with a null id, and we would not know which ticket we are looking for.
* The primary key restriction ensures this does not happen.
* When we have a primary key, we are certain that we can search for any record in the database and then modify or delete it, and no other record will be modified or deleted except the selected one.
* This allows us to maintain data integrity.
* 
**Adding a primary key to a new table**
To add a primary key to a new table, we simply need to specify it in the column definition.
For example:
```sql
CREATE TABLE boletas (
    id INT PRIMARY KEY,
    monto_de_la_boleta REAL,
    fecha_de_emision DATE
);
```
### Create a table with primary key
create table posts (id int primary key, title text, content text);
```sql
INSERT INTO posts VALUES
(1, 'Introducción', '¡Bienvenido al mundo de la programación!'),
(2, 'Primeros Pasos', 'Sumérgete en los conceptos básicos de la programación.'),
(3, 'Temas Avanzados', 'Explora conceptos y técnicas avanzadas en programación.');
```
### Autoincrement
Key ideas
1. The Primary Key restriction prevents null values from being entered and ensures that there are no duplicates in a specific column.
2. Practically, we can say that it is a combination of Unique and Not Null.
3. Autoincrement fields allow us to generate a unique value automatically for each record we insert into a table.
4. If a primary key field is an integer, it automatically becomes an autoincrement field in SQLite.

**What are autoincrement fields?**
Autoincrement fields are fields that generate a unique value automatically for each record we insert into a table.
Usually, the increment is 1 by 1.
So if we have a table like the following:

| id | monto de la boleta | fecha de emision |
| ---- | ---------------- | ---------------- |
| 1 | 10.000 | 2021-10-01 |
| 2 | 12.000 | 2021-10-02 |
| 3 | 16.000 | 2021-10-03 |

And we add a new record without specifying the 'id' field value, the database will generate a unique value for that field, which in this case would be 4.

Create a table with autoincrement field
```sql
create table usuarios (id integer primary key, nombre text not null, fecha_creacion date);
INSERT INTO usuarios (nombre, fecha_creacion) VALUES
('Ana', '2024-01-01'),
('Gonzalo', '2024-01-02'),
('Juan', '2024-01-03'),
('María', '2024-01-04');

> [!NOTE]
> the autoincrement word is optional in SQLite in the table definition. In fact, sometimes it can cause some issues.
```
### Autoincrement part 2
If a record is entered with a value greater than the current sequence, the database will update the sequence so that the next record has a value greater than the record we just inserted.
For example, if we have a table with the following records:
| id | nombre |
| ---- | ----- |
| 1 | Ana |
| 2 | Gonzalo |
| 3 | Juan |
Then we insert a new record with an id greater than the current sequence:
```sql
INSERT INTO usuarios (id, nombre) VALUES (10, 'María');
```
And then insert a new record without specifying the id:
```sql
INSERT INTO usuarios (nombre) VALUES ('Pedro');
```
We will get the following table:
| id | nombre |
| ---- | ----- |
| 1 | Ana |
| 2 | Gonzalo |
| 3 | Juan |
| 10 | María |
| 11 | Pedro |

Exercise:
create table transacciones (id integer primary key, monto real not null, fecha date);
```sql
INSERT INTO transacciones (monto, fecha) VALUES
(1000.00, '2024-01-01'),
(2000.00, '2024-01-02'),
(3000.00, '2024-01-03');
INSERT INTO transacciones VALUES (10, 4000.00, '2024-01-04');
INSERT INTO transacciones (monto, fecha) VALUES (5000.00, '2024-01-05');
```
### Primary key and text
1. The primary key is not exclusively LIMITed to numeric values; text data can also be used.
For example, consider a 'personas' table where we could use the email address as the primary key, as each individual has a unique email address.
2. In SQLite, fields that are of type INTEGER and are designated as PRIMARY KEY cannot contain null values.
However, unlike other database management systems like MySQL or PostgreSQL, when PRIMARY KEY is used with data types such as text or others, it is allowed for the value to be null.
Therefore, if we want a field to be both primary key and not null, we must specify it using the combination of PRIMARY KEY and NOT NULL.
Example:
```sql
CREATE TABLE posts (
    title text primary key not null
);
```
Create a table with primary key and not null constraint
```sql
create table personas (email text primary key not null, nombre text, apellido text);
INSERT INTO personas VALUES
('example1@example.com','John', 'Doe'),
('example2@example.com','Jane', 'Smith'),
('example3@example.com','Mike', 'Johnson');
```
### Foreign Key
A foreign key is a restriction that can be added to a column of a table to indicate that the values inserted into that column must exist in another table.
For example, if we have a table of people and a table of cars, we could add a 'persona_id' column to the cars table,
and add the foreign key restriction to indicate that the value of that column must exist in the people table.
This ensures that cars of non-existent people are not inserted or that people with assigned cars are not deleted, leaving cars without an owner.
People table:
| Column | Type | Restrictions |
| ---- | ----- | ----- |
| id | INTEGER | PRIMARY KEY |
| nombre | TEXT |  |
| apellido | TEXT | |

Cars table:
| Column | Type | Restrictions |
| ---- | ----- | ----- |
| id | INTEGER | PRIMARY KEY |
| patente | TEXT |  |
| persona_id | INTEGER | FOREIGN KEY (persona_id) REFERENCES personas(id) |

With the following data:
People:
| id | nombre | apellido |
| ---- | ----- | ----- |
| 1 | John | Doe |
| 2 | Jane | Smith |

Cars:
| id | patente | persona_id |
| ---- | ----- | ----- |
| 1 | ABC123 | 1 |
| 2 | DEF456 | 2 |

We can see that the car with patente ABC123 belongs to the person with id 1, and the car with patente DEF456 belongs to the person with id 2.
Additionally, the foreign key ensures that we cannot delete the person with id 1 while there is a car with persona_id 1.
Similarly, we cannot insert a car with persona_id 3, as there is no person with id 3.

**Adding the foreign key:**
To add a foreign key to an existing table, we must specify the FOREIGN KEY restriction followed by the column name and the table it references,
and finally the column of the referenced table. The syntax is as follows:
```sql
ALTER TABLE table_name ADD COLUMN column_name data_type REFERENCES reference_table(reference_column);
```
Example with the personas and autos tables:
```sql
ALTER TABLE autos ADD COLUMN persona_id INTEGER REFERENCES personas(id);
```
The foreign key must reference a column that has a primary key restriction.

Exercise:
Add a foreign key to the 'articulos' table so that the 'categoria_id' column references the 'id' column of the 'categorias' table.
```sql
ALTER TABLE articulos ADD COLUMN categoria_id INTEGER REFERENCES categorias(id);
```
### PK and FKs
Exercise:
We have the 'transacciones' and 'usuarios' tables with the following structures:

Transacciones table:
| Column | Type | Restrictions |
| ---- | ----- | ----- |
| id | INTEGER | PRIMARY KEY |
| monto | REAL |  |
| usuario_id | INTEGER | FOREIGN KEY (usuario_id) REFERENCES usuarios(id) |

Usuarios table:
| Column | Type | Restrictions |
| ---- | ----- | ----- |
| id | INTEGER | PRIMARY KEY |
| nombre | TEXT |  |
| apellido | TEXT | |

With the following data:
Transacciones:
| id | monto | usuario_id |
| ---- | ----- | ----- |
| 1 | 100 | 1 |
| 2 | 200 | 2 |
| 3 | 300 | 1 |

Usuarios:
| id | nombre | apellido |
| ---- | ----- | ----- |
| 1 | John | Doe |
| 2 | Jane | Smith |

* In this exercise, first we will try to create a transaction with a non-existent user to observe the error.
* Then, we will try to delete a user who has associated transactions to observe the error.
* Next, we will delete our previous queries and modify the 'transacciones' table to remove the foreign key.
* Only the foreign key should be removed, not the column.
> [!TIP] 
> This requires creating a temporary table, copying the data from the original table to the temporary table, deleting the original table, and renaming the temporary table to the original table name.
* Finally, transactions should be associated with the user with id 3, who does not exist, to demonstrate that without the FK, we can insert transactions without associated users.
```sql
INSERT INTO transacciones (monto, usuario_id) VALUES (300, 3);
DELETE FROM usuarios WHERE id = 1;
CREATE TABLE transacciones2 (id INTEGER PRIMARY KEY, monto REAL, usuario_id INTEGER);
INSERT INTO transacciones2 (id, monto, usuario_id) SELECT id, monto, usuario_id FROM transacciones;
DROP TABLE transacciones;
ALTER TABLE transacciones2 RENAME TO transacciones;
UPDATE transacciones SET usuario_id = 3;
```
## Queries in multiple tables
Key ideas
1. The JOIN clause allows combining data from multiple tables into a single result table.
2. To perform the join, it is necessary to specify the tables to be joined and the corresponding join condition.
Example:
```sql
SELECT * FROM usuarios INNER JOIN datos_contacto ON email1 = email2;
```
Exercise:
Using what we have learned, select all users along with their notes. Observe the results before proceeding.
```sql
SELECT * FROM usuarios INNER JOIN notas ON usuarios.email = notas.email;
```
### Multiple tables: Using attributes with the same name
When the column name is the same in both tables, we need to specify the table name to avoid ambiguity.
To fix this, we DO NOT need to change the column name; we simply need to specify which table the attribute belongs to.
For example, we can do the following:
```sql
SELECT * FROM usuarios JOIN datos_contacto ON usuarios.email = datos_contacto.email;
```
**Alias**
In SQL, we can use aliases to refer to tables in a shorter way.
For example, if we want to refer to the 'usuarios' table as 'u' and the 'datos_contacto' table as 'dc', we can do the following:
```sql
SELECT * FROM usuarios u JOIN datos_contacto dc ON u.email = dc.email;
```
Exercise:
```sql
SELECT * FROM usuarios u INNER JOIN notas n ON u.email = n.email;
```
### Selecting some attributes
We can select the data from each table that we want to display in the query.

**Selecting some attributes**
Just as when working with a single table, we can select only the attributes we want to display in the query.
When we have two tables, we can select all the attributes from one table and only some from the other as follows:

Example:
```sql
SELECT table1.*, table2.attribute1, table2.attribute2 FROM table1 JOIN table2 ON table1.id = table2.id;
```
This way, we select everything FROM the 'usuarios' table and only the phone numbers FROM the 'datos_contacto' table,
so we don't have two email columns with duplicate data.
```sql
SELECT usuarios.*, datos_contacto.telefono FROM usuarios JOIN datos_contacto ON usuarios.email = datos_contacto.email;
```
Exercise:
```sql
SELECT usuarios.*, notas.notas FROM usuarios JOIN notas ON usuarios.email = notas.email;
```
### JOIN without results
1. The JOIN clause only combines records that have a common key in both tables.
2. There are other types of JOIN that we will review later.
3. The answer is quite simple: if there is no common data between both tables, we will not get results.
4. There is an email column in both tables, but the email data in 'usuarios' is not in 'datos_contacto', so the result is empty.
```sql
SELECT usuarios.*, datos_contacto.telefono FROM usuarios JOIN datos_contacto ON usuarios.email = datos_contacto.email;
```
### Order of clauses
Clauses have a specific order that we must follow for the query to work correctly.
| Command | sequence |
| ----- | ----- |
| SELECT | Select these data | 
| FROM | From this table |
| JOIN | Join them with this table |
| WHERE | Filter the values that meet this condition |
| GROUP BY | Group the results by this criterion |
| HAVING | Filter by these grouped criteria |
| ORDER BY | Sort the results by this other criterion |
| LIMIT | LIMIT the results to this amount |

Exercise:
Given the following tables, select all the information of the user 'juan.perez@example.com'.
```sql
SELECT * FROM usuarios JOIN notas ON usuarios.email = notas.email WHERE usuarios.email = 'juan.perez@example.com';
```
### Grouping by multiple columns
Using GROUP BY and aggregation functions in queries with multiple tables
Just as in queries on a single table, we can use aggregation functions and grouping in queries on multiple tables.
Suppose we have two tables: a table called 'clientes' and another table called 'pedidos'.
Clientes table:
| cliente_id | nombre |
| ----- | ----- |
| 1 | Juan Pérez |
| 2 | Ana Gómez |
| 3 | Luis Fernández |

Pedidos table:
| cliente_id | nombre | nombre |
| ----- | ----- | ----- |
| 101 | 1 | 2023-01-15 | 
| 102 | 2 | 2023-02-20 | 
| 103 | 1 | 2023-03-10 | 
| 104 | 3 | 2023-04-22 | 
| 105 | 2 | 2023-05-18 | 
| 106 | 1 | 2023-06-30 | 

To show the total number of orders placed by each client, we can use the following SQL query:
```sql
SELECT nombre, COUNT(p.pedido_id) AS total_pedidos FROM clientes c JOIN pedidos p ON c.cliente_id = p.cliente_id GROUP BY c.cliente_id, nombre;
```
**Some important details to keep in mind:**
* If there are columns with the same name, we need to specify the table name before the column name.
* When grouping data, we must keep in mind that the columns not in the GROUP BY clause must be used with aggregation functions.
* Therefore, in these types of exercises, select * is not recommended, especially if the table has many columns.

Exercise:
We have two tables: 'Productos' and 'Ventas'.
Create a query that shows the most sold product and the total number of units sold of that product.
The column that shows the total number of units sold should be called "total_vendido".
Hint: Remember to use ORDER BY and LIMIT - we must use DESC in the ORDER BY because we are asked for the highest value.
```sql
SELECT nombre, SUM(v.cantidad) AS total_vendido FROM productos p JOIN ventas v ON p.productoid = v.productoid GROUP BY p.productoid, nombre ORDER BY total_vendido DESC LIMIT 1;
```
## Cardinality
### One-to-one relationships
Key ideas
1. The cardinality of a relationship is the number of elements of one table that can be related to those of another table.
2. Depending on how the relationships between tables are modeled, one-to-one, one-to-many, or many-to-many relationships can be had.
3. In a one-to-one relationship, a record of one table can only be related to a record of the other table.

**What is the cardinality of a relationship?**
The cardinality of a relationship is the number of elements of one table that can be related to those of another table.
Relationships between tables can be classified into three main types according to their cardinality.
In this exercise, we will look at the one-to-one relationship, denoted as 1:1.
In this relationship, each record of one table is related to a unique record of another table.
For example, each person can have a unique passport, and each passport belongs to a unique person.

Exercise:
Create a query that shows all the information of the vehicle registrations along with their corresponding registration.
```sql
SELECT * FROM vehiculos v JOIN matriculas m ON v.id = m.vehiculo_id;
```
### One-to-many relationships
In a one-to-many relationship, a record of one table can be related to one or several records of another table.
For example, we could have a table of employees and another of departments.
Each employee belongs to a single department, but a department can have several employees.

Exercise:
Create a query that shows all the information of the countries along with their corresponding continent.
Observe within the results that a country belongs to a single continent, but a continent can have several countries.
```sql
SELECT p.pais_id, p.nombre, p.continente_id, c.continente_id, c.nombre FROM continentes c JOIN paises p ON c.continente_id = p.continente_id;
```
### Many-to-many relationships
In a many-to-many relationship, a record of one table can be related to one or several records of another table, and vice versa.
Many-to-many relationships
In relational databases, with two tables, we can only achieve one-to-one or one-to-many relationships.
To achieve many-to-many relationships, an intermediate table with certain characteristics that we will study later is needed.

Solved exercise:
We have a system that stores information about teachers and students.
Each teacher can have several students, and each student can have several teachers.
To achieve this, we have a 'profesores' table, a 'alumnos' table, and a 'profesores_alumnos' table that relates teachers to students.

Profesores table:
| profesor_id | nombre |
| ----- | ----- |
| 1 | Ana |
| 2 | Pedro |
| 3 | Luis |

Alumnos table:
| alumno_id | nombre |
| ----- | ----- |
| 1 | Marta |
| 2 | Elena |
| 3 | Juan |

Profesores_alumnos table:
| profesor_id | alumno_id |
| ----- | ----- |
| 1 | 1 |
| 1 | 2 |
| 2 | 1 |

By joining the 'profesores' and 'profesores_alumnos' tables with a JOIN, we get:
| profesor_id | nombre | alumno_id |
| ----- | ----- | ----- |
| 1 | Ana | 1 |
| 1 | Ana | 2 |
| 2 | Pedro | 1 |

By joining the previous table with the 'alumnos' table, we get:
| profesor_id | nombre | alumno_id | alumno_id |
| ----- | ----- | ----- | ----- |
| 1 | Ana | 1 | Marta |
| 1 | Ana | 2 | Elena |
| 2 | Pedro | 1 | Marta |

Where we can see that for each record in the 'profesores' table, there are one or more records in the 'profesores_alumnos' table,
and for each record in the 'profesores_alumnos' table, there is a record in the 'alumnos' table.

To join more than 2 tables, a join can be made for each table to be joined.
```sql
SELECT * FROM profesores JOIN profesores_alumnos ON profesores.profesor_id = profesores_alumnos.profesor_id JOIN alumnos ON profesores_alumnos.alumno_id = alumnos.alumno_id;
```
Exercise:
We have a database with a catalog of objects and colors.
Each object can have several colors, and each color can be associated with several objects.
```sql
SELECT * FROM objetos o JOIN objetos_colores oc ON o.objeto_id = oc.objeto_id JOIN colores c ON c.color_id = oc.color_id;
```
### Characteristics of the intermediate table
Key ideas
1. In a many-to-many relationship, a record of one table can be related to one or several records of another table, and vice versa.
2. Relational databases do not allow many-to-many relationships directly, so an intermediate table that relates the main tables is needed.
3. The intermediate table must have a column that is a foreign key for each of the main tables.

**Understanding the intermediate table**
If we have two tables, A and B, that we want to relate in a many-to-many manner, we need an intermediate table C that relates both tables.

Exercise:
Add records to the 'profesores_alumnos' table so that Julia has Elena as a student and Pedro has Juan as a student.
Then show all columns of the three tables.
```sql
INSERT INTO profesores_alumnos VALUES (1, 2), (2, 3);
SELECT * FROM profesores p JOIN profesores_alumnos pa ON p.profesor_id = pa.profesor_id JOIN alumnos a ON a.alumno_id = pa.alumno_id;
```
### Without uniqueness restriction
Key ideas
1. The intermediate table must have a column that is a foreign key for each of the main tables.
2. If there is a uniqueness restriction, it ensures that there are no duplicate records in the intermediate table.
3. This is achieved with a composite primary key of the foreign keys of the main tables.

### Uniqueness restriction
Suppose we have a library management system that includes three main tables: 'Libros', 'Usuarios', and 'Pedidos'.
The 'Pedidos' table acts as an intermediate table to handle the many-to-many relationship between 'Libros' and 'Usuarios'.
In this system, a book can be requested by multiple users, and each user can request multiple books.
Before building the intermediate table, we need to ask an important question: Can a user request the same book more than once?

If the answer is yes, then we do not need a uniqueness restriction in the intermediate table.
If the answer is no, then we must ensure that there are no duplicate records in the intermediate table, and this is done with a uniqueness restriction.
In this example, it makes sense that a user can request the same book more than once, so our intermediate table would have the following structure:
| libro_id | usuario_id |
| ----- | ----- |
| 1 | 1 |
| 1 | 1 |
| 2 | 2 |
| 2 | 2 |
| 3 | 1 |
Within the table, we can see that user 1 has requested book 1 twice, which is not a problem if there is no uniqueness restriction.

Exercise:
Select all users who have requested the same book more than once.
The columns to display are usuario_id, libro_id, and veces, where veces is the number of times the user has requested the book.
> [!TIP]
> Group by libro_id and usuario_id and count how many records there are for each group.
Reflect on whether to use where or HAVING to filter the results.
```sql
SELECT usuario_id, libro_id, COUNT(libro_id) AS veces FROM pedidos GROUP BY libro_id, usuario_id HAVING veces > 1;
```
**With uniqueness restriction**
Suppose we have a system that stores information about projects and employees.
Each employee can work on multiple projects, and each project can have multiple employees working on it. To handle this, we have an 'empleados' table, a 'proyectos' table, and an 'empleados_proyectos' table that relates employees to projects.
Before building the intermediate table, we need to ask an important question: Can an employee be in the same project more than once?
* If the answer is yes, then we do not need a uniqueness restriction in the intermediate table.
* If the answer is no, then we must ensure that there are no duplicate records in the intermediate table, and this is done with a uniqueness restriction.
In this case, an employee cannot be assigned to the same project more than once.
To prevent this from happening, when creating the table, we will add a composite primary key of the foreign keys of the main tables.
```sql
CREATE TABLE Empleados_Proyectos (
    empleado_id INT,
    proyecto_id INT,
    PRIMARY KEY (empleado_id, proyecto_id),
    FOREIGN KEY (empleado_id) REFERENCES Empleados(id),
    FOREIGN KEY (proyecto_id) REFERENCES Proyectos(id)
);
```
This way, if a record with an employee and a project that already exists in the table is attempted to be added, an error will be generated,
ensuring that there are no duplicate records in the intermediate table.

Exercise:
Create a query that selects all employees along with the number of projects assigned to each one, demonstrating that there are no duplicate records in the intermediate table.
The columns of the query should be 'nombre', 'puesto', and 'cantidad_proyectos'.
```sql
SELECT e.nombre, e.puesto, COUNT(ep.proyecto_id) AS cantidad_proyectos
FROM empleados_proyectos ep
JOIN empleados e ON e.id = ep.empleado_id
GROUP BY e.id
ORDER BY cantidad_proyectos DESC;
```
## Types of Joins
### Inner Join
Key ideas
1. The JOIN clause allows us to combine data from multiple tables into a single result table.
2. There are several types of joins, and when the type of join is not specified, INNER JOIN is used by default.
3. The difference between the types of joins lies in how records that do not have a common key in both tables are handled.
4. INNER JOIN is the default option.
5. So far, we have used INNER JOIN, but we have not specified it.
6. If the type of JOIN is not specified, INNER JOIN is used by default.
This implies that:
```sql
SELECT * FROM usuarios JOIN datos_contacto ON usuarios.email = datos_contacto.email
```
is the same as:
```sql
SELECT * FROM usuarios INNER JOIN datos_contacto ON usuarios.email = datos_contacto.email
```
**What does INNER JOIN do?**
INNER JOIN combines records from both tables based on a join condition.
Only records with a common key in both tables are included.
If there is no common key, the records are not included in the final result.

Exercise
Join the tables using JOIN (or INNER JOIN) to get all the records from both tables.
Look at the tables before doing the exercise and pay special attention to Francisco, who has no notes in the system.
Francisco does not appear because he has no record in notes associated with the email in usuarios.
```sql
SELECT * FROM usuarios u INNER JOIN notas n ON u.email = n.email;
```
### Inner Join with Venn Diagram
Key ideas
1. Venn diagrams are a visual way to represent sets and operations between them.
2. We can use Venn diagrams to visualize what happens with the records of the tables when performing different types of JOINs.
3. In a Venn diagram, an INNER JOIN is visualized as the intersection between two circles.

**Venn Diagrams**
A Venn diagram allows us to visualize sets and operations between them.
These diagrams consist of circles representing sets of data.
In them, the elements that the different sets of data have in common are represented in the intersection of the circles.
When working with databases, we can use these diagrams to visualize the records that would be obtained when performing different types of JOINs.
Especially, they will be useful to visualize the results of a join operation.
To do this, we will put the join keys of the tables as elements.
The elements that are only in the first table will be represented in one circle, the elements that are only in the second table will be represented in another circle,
and the elements that are in both tables will be represented in the intersection of both circles.

Exercise
On a piece of paper, draw a Venn diagram that shows the records that would be obtained when performing an INNER JOIN between the tables 'actores' and 'peliculas'.
In the code editor below, perform a query that shows the names of the actors and the titles of the movies they have acted in.
Does the result of the code match your drawing? YES
```sql
SELECT a.nombre, p.titulo FROM actores a INNER JOIN peliculas p ON a.actor_id = p.actor_id;
```
### Left Join
When using a LEFT JOIN, if the key is in the left table but not in the right table, the record from the left table will appear in the final result with NULL values in the fields of the right table.

**Inner join vs. Left join**

To illustrate the difference, we will work with the tables 'usuarios' and 'notas'.
| usuarios | notas |
| -------- | ----- |
| email    | email |
| nombre   | notas |
| edad     |  |

Table 'usuarios'
| email | nombre | edad |
| -------- | ----- | ----- |
| juan.perez@example.com | Juan Pérez | 30 |
| maria.gonzalez@example.com | Maria González | 25 |
| john.doe@example.com | John Doe | 40 |
| francisco@example.com | Test User | 22 |

Table 'notas'
| email | notas |
| -------- | ----- |
| juan.perez@example.com | 90 |
| maria.gonzalez@example.com | 100 |
| john.doe@example.com  | 80 |
| juan.perez@example.com | 100 |
| maria.gonzalez@example.com | 100 |

If we do an INNER JOIN between the tables 'usuarios' and 'notas' using the email field as the key, we will get the following records:
```sql
SELECT u.email, u.nombre, u.edad, n.notas FROM usuarios u INNER JOIN notas n ON u.email = n.email;
```
| email | nombre | edad | edad |
| ----- | ----- | ----- | ----- |
| juan.perez@example.com | Juan Pérez | 30 | 90 |
| juan.perez@example.com | Juan Pérez | 30 | 100 |
| maria.gonzalez@example.com | Maria González | 25 | 100 |
| maria.gonzalez@example.com | Maria González | 25 | 100 |
| john.doe@example.com | John Doe | 40 | 80 |

Here we see that francisco@example.com does not appear in the final result, as he has no notes in the system.
However, if we use a LEFT JOIN between the tables 'usuarios' and 'notas', as follows:
```sql
SELECT u.email, u.nombre, u.edad, n.notas FROM usuarios u LEFT JOIN notas n ON u.email = n.email;
```
We will get the following records:
| email | nombre | edad | edad |
| ----- | ----- | ----- | ----- |
| juan.perez@example.com | Juan Pérez | 30 | 90 |
| juan.perez@example.com | Juan Pérez | 30 | 100 |
| maria.gonzalez@example.com | Maria González | 25 | 100 |
| maria.gonzalez@example.com | Maria González | 25 | 100 |
| john.doe@example.com | John Doe | 40 | 80 |
| francisco@example.com | Test User | 22 | NULL |

By doing a LEFT JOIN, the record of francisco@example.com appears in the final result with NULL values in the fields of the 'notas' table,
since Francisco has no notes in the system, but he is in the 'usuarios' table.

Performing a LEFT JOIN
The syntax for using LEFT JOIN is similar to INNER JOIN:
```sql
SELECT * FROM table1 LEFT JOIN table2 ON table1.attribute = table2.attribute
```
Exercise
We have a table called 'empleados' and another called 'departamentos'.
Using what we have learned, select all employees along with their corresponding departments, including employees who have not yet been assigned to any department.
Both tables have the email column.

| empleados |                departamentos |
|-----------|               ---------------|
| email     |                email         |
| nombre    |                departamento  |
| edad      |               
             
```sql
SELECT * FROM empleados e LEFT JOIN departamentos d ON e.email = d.email;
```
### Left Join with Venn Diagram
In the Venn diagram, a LEFT JOIN is visualized as the left set.
When performing a Left Join between table A and table B, all records from table A appear in the results, even those without a corresponding key in table B.
We can visualize the Left Join as the left set in a Venn diagram.

**Analyzing the result**
Additionally, by looking at the Venn diagram, we can observe the result of the Left Join operation between the tables 'productos' and 'ventas'.
In it, we see:
* The records with id 1, 2, and 3 from the 'productos' table appear in the Left Join results, as all three are in the left set.
* The record with id 4 from the 'ventas' table does not appear in the Left Join results, as it is not in the left set.
* Records 1 and 2 do not appear with data from the 'ventas' table, as they are only in the left table.
* Record 3 appears with data from the 'ventas' table, as it is the only one found in both tables.

Exercise
We have a table called 'Personas' that contains the names of people and the id of the profession they belong to.
We want to get a list of all professions and the people who belong to each of them.
If a profession has no associated people, the profession name should be shown with NULL in the 'Nombre' field.
The resulting table should only contain two columns: 'Profesion' and 'Nombre'.
```sql
SELECT pr.profesion, pe.nombre FROM profesion pr LEFT JOIN personas pe ON pr.id = pe.profesion_id;
```
### Right Join
When using RIGHT JOIN, if the key is in the right table but not in the left table, the record from the right table will appear in the final result with NULL values in the fields of the left table.

RIGHT JOIN functions similarly to LEFT JOIN but reverses the order of the tables.
While LEFT JOIN returns all rows from the left table and the corresponding matches from the right table (filling with NULL if there is no match),
RIGHT JOIN does the opposite: it returns all rows from the right table and the corresponding matches from the left table.

To perform a RIGHT JOIN in SQL, the following syntax is used:
```sql
SELECT * FROM table1 RIGHT JOIN table2 ON table1.attribute = table2.attribute;
```
Exercise
We are asked to select all records from the departments of all offices along with their corresponding employees, including those departments that have no associated employees.
Both tables contain the email column.
```sql
SELECT * FROM empleados e RIGHT JOIN departamentos d ON e.email = d.email;
```
### Left Join and Right Join
Key ideas
1. When using LEFT JOIN, if the key is in the left table but not in the right table, the record from the left table will appear in the final result with NULL values in the fields of the right table.
2. When using RIGHT JOIN, if the key is in the right table but not in the left table, the record from the right table will appear in the final result with NULL values in the fields of the left table.
3. LEFT JOIN and RIGHT JOIN are reflections of each other.
4. Using LEFT JOIN or RIGHT JOIN simply depends on which table you want to name first.
```sql
SELECT * FROM table1 LEFT JOIN table2 ON table1.id = table2.id
```
is practically the same as:
```sql
SELECT * FROM table2 RIGHT JOIN table1 ON table2.id = table1.id
```
**LEFT JOIN and RIGHT JOIN are reflections of each other.**
However, there is a small difference when used in conjunction with select, as the attributes of the first table will be displayed first.
To get the results in the same order, we can simply specify the order we want.
```sql
SELECT usuarios.*, notas.* FROM Notas RIGHT JOIN Usuarios ON Notas.email = Usuarios.email;
```
Exercise
Select all records of all products (table 'productos') along with their prices (table 'precios'), including products that have not been assigned a price.
The tables are related by the 'producto_id' column.
```sql
SELECT * FROM productos p LEFT JOIN precios pre ON p.producto_id = pre.producto_id;
```
### Identifying types of join
Key ideas:
1. There are multiple types of JOIN
2. We must know how to identify the type of JOIN to use in a query based on the request
3. Useful guide to identify the type of join:

**INNER JOIN**
When to use it: When you need only the rows where there are matches in both tables.
Result: Returns only the rows with corresponding data in both tables.

**LEFT JOIN (or LEFT OUTER JOIN)**
When to use it: When you need all rows from the left table and the matching rows from the right table.
Result: Returns all rows from the left table and the matches from the right table, with NULLs where there are no matches.

**RIGHT JOIN (or RIGHT OUTER JOIN)**
When to use it: When you need all rows from the right table and the matching rows from the left table.
Result: Returns all rows from the right table and the matches from the left table, with NULLs where there are no matches.

Exercise
We have a database with two main tables: 'autores' and 'libros'.
Create a query to obtain information showing the name of the author along with the title of the book they have written.
The query should include only those books that have assigned authors.
The columns of the query should be called: 'nombre_autor' and 'titulo_libro'.
```sql
SELECT a.nombre AS nombre_autor, l.titulo AS titulo_libro FROM autores a INNER JOIN libros l ON a.id = l.id_autor;
```
Exercise
We have a database with two main tables: 'empleados' and 'proyectos'.
Create a query to obtain detailed information that shows the name of the employee along with the name of the project they participate in.
The 'empleados' table has the following fields:
id (unique identifier of the employee)
nombre (name of the employee)
id_proyecto (identifier of the project in which the employee participates)
The 'proyectos' table has the following fields:
id (unique identifier of the project)
nombre_proyecto (name of the project)
The result should have columns with the following names and include all employees, even if they are not assigned to any project.
nombre_empleado    nombre_proyecto
```sql
SELECT e.nombre AS nombre_empleado, p.nombre_proyecto FROM empleados e LEFT JOIN proyectos p ON e.id_proyecto = p.id;
```
Exercise
We have a database with two main tables: 'empleados' and 'proyectos'.
Obtain a list of all projects along with the names of the employees assigned to each project, including those projects that have no assigned employees.
The result should consist only of the columns 'nombre_empleado' and 'nombre_proyecto', corresponding to the name of the employee and the name of the project from their respective tables, including those without assigned employees (employee NULL).
```sql
SELECT e.nombre AS nombre_empleado, p.nombre_proyecto FROM empleados e RIGHT JOIN proyectos p ON e.id_proyecto = p.id;
```
### Full outer join
When using FULL OUTER JOIN, all records from both tables are obtained, including non-matching records.

Full outer join is a combination of LEFT JOIN and RIGHT JOIN.
It returns all records from both tables, including non-matching records.

**Implementing full outer join in SQLite**
The SQLite database engine does not support FULL OUTER JOIN operations.
However, the same effect can be achieved with the following syntax:
```sql
SELECT * 
FROM tableA LEFT JOIN tableB 
ON tableA.column_name = tableB.column_name 
UNION 
SELECT * 
FROM tableA RIGHT JOIN tableB 
ON tableA.column_name = tableB.column_name;
```
Exercise
Given the previously mentioned 'empleados' and 'departamentos' tables, write a query that returns all matching and non-matching records between the 'empleados' and 'departamentos' tables.
```sql
SELECT * 
FROM empleados e LEFT JOIN departamentos d 
ON e.id_departamento = d.id_departamento 
UNION 
SELECT * 
FROM empleados e RIGHT JOIN departamentos d 
ON e.id_departamento = d.id_departamento;
```
### Union vs Union all
If we look at the diagram carefully, we will realize that if we simply combine the results of a LEFT JOIN and a RIGHT JOIN, we could be duplicating the records at the intersection point.
This is WHERE we need to remember the difference between UNION and UNION ALL.
UNION eliminates duplicate records, while UNION ALL does not.
Therefore, if we combine the results of a LEFT JOIN and a RIGHT JOIN, we should use UNION to avoid duplicates.
```sql
SELECT * 
FROM tableA LEFT JOIN tableB 
ON tableA.column_name = tableB.column_name 
UNION 
SELECT * 
FROM tableA RIGHT JOIN tableB 
ON tableA.column_name = tableB.column_name;
```
Exercise
Given the 'clientes' and 'pedidos' tables, create a query that returns all matching and non-matching records between the 'clientes' and 'pedidos' tables.
```sql
SELECT * 
FROM clientes c LEFT JOIN pedidos p 
ON c.id_cliente = p.id_cliente 
UNION 
SELECT * 
FROM clientes c RIGHT JOIN pedidos p 
ON c.id_cliente = p.id_cliente;
```
### Left excluding join
A left excluding join is a combination of left join with a where clause to show records from the left table that do not have matches in the right table.
In this example, if we want to obtain students without grades, we can do it with a left excluding join.
```sql
SELECT * 
FROM alumnos a LEFT JOIN calificaciones c 
ON a.id = c.alumno_id 
WHERE c.calificacion IS NULL;
```
And as a result, we would obtain:
| id | nombre | apellido | id | alumno_id | calificacion |
| ----- | ----- | ----- | ----- |----- |----- |
| 3 | Pedro | Lopez |  |  |  |

Exercise
Create an SQL query that shows all the data from the mentioned tables of people without a profession.
```sql
SELECT * 
FROM personas pe LEFT JOIN profesion pr 
ON pr.id = pe.profesion_id 
WHERE pe.profesion_id IS NULL;
```
### Right excluding join
A right excluding join is a combination of right join with a where clause to show records from the right table that do not have matches in the left table.

How does a right excluding join work?
Right excluding join is very similar to Left excluding Join.
A right excluding join query delivers records that do not match with the left table.
```sql
SELECT * 
FROM tableA RIGHT JOIN tableB 
ON tableA.name = tableB.name 
WHERE tableA.name IS NULL;
```
It is important to note that in addition to changing the JOIN, we need to change the where clause and substitute tableA with tableB.

Practical example
In a database, we have the 'calificaciones' and 'cursos' tables.
We are asked to obtain all the information of the courses without grades.
To solve this with a right excluding join, the query would be:
```sql
SELECT * 
FROM calificaciones c RIGHT JOIN cursos cu 
ON cu.id = c.curso_id 
WHERE c.id IS NULL;
```
As a result, we would obtain:
| id | alumno_id | curso_id | calificacion | id | nombre | docente_id |
| ----- | ----- | ----- | ----- | ----- | ----- | ----- |
| 3 | Arte | 1 |  |  |  |  |

Exercise
Use a right join to obtain the teachers who do not have an assigned course.
```sql
SELECT * 
FROM cursos c RIGHT JOIN docentes d 
ON c.docente_id = d.id 
WHERE c.docente_id IS NULL;
```
### Full outer excluding join
Full Outer Excluding Join is a combination of FULL OUTER JOIN with a where clause to show records that do not have matches in both tables.
Although SQLite does not directly support FULL OUTER JOIN, we can perform a FULL OUTER EXCLUDING JOIN using a combination of LEFT EXCLUDING JOIN and RIGHT EXCLUDING JOIN.
```sql
SELECT * 
FROM tabla_A LEFT JOIN tabla_B 
ON tabla_A.clave = tabla_B.clave 
WHERE tabla_B.clave IS NULL 
UNION 
SELECT * 
FROM tabla_A RIGHT JOIN tabla_B 
ON tabla_A.clave = tabla_B.clave 
WHERE tabla_A.clave IS NULL;
```
This query will give us all records from table_A that do not have matches in table_B, and all records from table_B that do not have matches in table_A.

Exercise
Write an SQL query that implements a FULL OUTER EXCLUDING JOIN between the 'empleados' and 'departamentos' tables.
This query should show both employees who are not assigned to any existing department and departments that do not have assigned employees.
Once you have written the query, execute it and analyze the results and answer the following questions:
Why does Carlos appear in the query result? - because he does not have an assigned department
Why does the Marketing department appear in the result? - because it does not have assigned employees
How is this result different from a regular FULL OUTER JOIN? - it shows all non-related records from both tables, even when one of the tables does not have a created record but is associated.
```sql
SELECT e.id_empleado, e.nombre, e.id_departamento, d.departamento 
FROM empleados e LEFT JOIN departamentos d 
ON e.id_departamento = d.id_departamento 
WHERE d.id_departamento IS NULL 
UNION 
SELECT e.id_empleado, e.nombre, d.id_departamento, d.departamento 
FROM empleados e RIGHT JOIN departamentos d 
ON e.id_departamento = d.id_departamento 
WHERE e.id_empleado IS NULL;
```
> [!NOTE] 
> to avoid duplicate columns, we must specify it in the select, but we must ensure that in the LEFT we show the id of the left table and in the RIGHT the id of > the right table.

### Natural Join
Key ideas
1. Natural Join is a JOIN that additionally assumes columns with the same name are the join columns.
2. The advantage of using NATURAL JOIN is that it simplifies writing the query, but it is only useful when the join columns have the same name and data type in both tables.

NATURAL JOIN is a type of join in SQL that is used to combine two tables using columns with the same name.
Its syntax is as follows:
```sql
SELECT columns 
FROM table1 NATURAL JOIN table2;
```
As it is expected that the columns have the same name, it is not necessary to specify it.

Let's see an example:
Suppose we have two tables, 'clientes' and 'pedidos'
In this case, both tables have a column 'id_cliente', so, when doing a Natural Join, this will be the column used to join the tables.
```sql
SELECT * 
FROM clientes NATURAL JOIN pedidos;
```
id_cliente  nombre  email           id_pedido  producto    cantidad
1           Juan    juan@example.com 1          Laptop      1
2           María   maria@example.com 2          Tablet      2
3           Pedro   pedro@example.com 3          Smartphone  1

Exercise
Make a query using NATURAL JOIN that shows the product name, quantity sold, and sale date of each sold product.
Use the original column names.
```sql
SELECT p.nombre, v.cantidad, v.fecha 
FROM productos p NATURAL JOIN ventas v;
```
### Natural Left Join
Just as for JOIN the INNER option is implicit, for NATURAL JOIN the INNER option is also implicit.
One can use NATURAL INNER JOIN, NATURAL LEFT JOIN, NATURAL RIGHT JOIN.

Natural Left Join
NATURAL LEFT JOIN performs a LEFT JOIN using columns with the same name in both tables.
Its use is as follows:
```sql
SELECT columns 
FROM table1 NATURAL LEFT JOIN table2;
```
Exercise
Given the provided tables, create a report that selects all students (their names), enrolled courses, and enrollment dates.
If a student is not enrolled in any courses, the student should be shown without enrollment data.
```sql
SELECT e.nombre, i.curso, i.fecha 
FROM estudiantes e NATURAL LEFT JOIN inscripciones i;
```
### Self Join
Key ideas
A self join is used to combine a table with itself.
Self join is not a specific type of join, it is just the term used to refer to the operation of combining a table with itself.
To achieve the self join, other types of joins such as INNER JOIN or LEFT JOIN are used.

Self Join
A self join is a type of join in SQL that is used to combine a table with itself.
It is used when we want to relate rows of a table with other rows of the same table.
Although the term "self join" refers to this self-relation, in practice other types of joins such as INNER JOIN or LEFT JOIN are used to perform this operation.

Example
Suppose we have a table called 'empleados' with the following structure:
id_empleado  nombre  id_supervisor
1            Juan    NULL
2            María   1
3            Pedro   1
4            Ana     2
5            Luis    2

Use of Self Join
To get the name of all employees along with the name of their supervisor, we can use a self join as follows:
```sql
SELECT e1.nombre AS nombre_empleado, e2.nombre AS nombre_supervisor 
FROM empleados e1 LEFT JOIN empleados e2 
ON e1.id_supervisor = e2.id_empleado;
```
nombre_empleado  nombre_supervisor
Juan             NULL
María            Juan
Pedro            Juan
Ana              María
Luis             María

In this example, we are obtaining the name of each employee (e1.nombre) and the name of their supervisor (e2.nombre).
The self join is performed by joining the 'empleados' table with itself (empleados e1 LEFT JOIN empleados e2), using the 'id_supervisor' column of e1 and the 'id_empleado' column of e2.
LEFT JOIN is used instead of INNER JOIN to include employees without a supervisor, as the query required the names of all employees.

Exercise
Given the 'clientes' table with the following data:
id_cliente  nombre  id_cliente_referente
1           Juan    NULL
2           María   1
3           Pedro   1
4           Ana     2
5           Luis    2

Write a query that shows the name of all clients along with the name of their referring client.
The columns of the resulting table should be called 'nombre_cliente' and 'nombre_cliente_referente'.
```sql
SELECT c1.nombre AS nombre_cliente, c2.nombre AS nombre_cliente_referente 
FROM clientes c1 LEFT JOIN clientes c2 
ON c1.id_cliente_referente = c2.id_cliente;
```
Exercise
Given the 'amigos' table that contains information about connected friends on a social network.
id_amigo  nombre  id_amigo_conectado
1         Carlos  NULL
2         Laura   1
3         Miguel  1
4         Ana     2
5         Luis    3

Write an SQL query using self join to get the name of each friend along with the name of their connected friend.
Only show friends who have a connected friend.
The columns of the resulting table should be called 'nombre' and 'nombre_amigo_conectado'.
> [!TIP] 
> Which type of join would you use to get the connected friends? Inner Join to show only those with connected friends
> [!TIP]
> Which columns of the 'amigos' table relate to each other? ID_Amigo and ID_amigo_conectado
```sql
SELECT a1.nombre, a2.nombre AS nombre_amigo_conectado 
FROM amigos a1 INNER JOIN amigos a2 
ON a1.id_amigo_conectado = a2.id_amigo;
```
### Cross Join
Key ideas
Cross Join generates the Cartesian product of two tables.
Cross Join does not require a join condition, as it combines all rows from the first table with all rows from the second table.

Cross Join
Cross Join, also known as Cartesian product, combines each row from the first table with each row from the second table, generating a result set that is the Cartesian product of both tables.
It is useful when you want to combine all rows from one table with all rows from another table.
The syntax of Cross Join is as follows:
```sql
SELECT columns 
FROM table1 CROSS JOIN table2;
```
Example
Suppose we have two tables, one of geometric shapes and another of colors.
We are asked to generate a query that shows all shapes along with all possible colors.
```sql
SELECT nombre_figura, nombre_color 
FROM figuras CROSS JOIN colores;
```
Exercise
Given a table of numbers and a table of suits.
Generate a query that shows all numbers along with all possible suits, simulating a deck of cards ordered by numbers from lowest to highest and suits in alphabetical order.
The columns of the resulting table should be called 'numero' and 'pinta'.
```sql
SELECT * 
FROM numeros CROSS JOIN pintas 
ORDER BY numero, pinta;
```
### Join with aggregate functions
Key ideas:
Combining data from multiple tables: The JOIN clause allows us to combine rows from two or more tables.
Choosing the type of JOIN: The choice of JOIN type depends on whether we want to count rows without matches; if so, we use LEFT JOIN, otherwise, we use INNER JOIN.
Grouping data: The use of GROUP BY allows us to group rows that have the same value in one or more columns.
Aggregate functions: On grouped data, we can use aggregate functions such as SUM(), COUNT(), and AVG().

What are we going to learn?
In these exercises, we will work with very common problems in data analysis, where we need to combine data from multiple tables, group them, and obtain information from aggregated data.
Throughout these exercises, we will learn to identify which type of JOIN to use for each case.

Scenario
We have a table of products and a table of sales.
We want to make a query that shows each product with the number of sales made.

Table productos
id  nombre
1   smartphone
2   tablet
3   camiseta
4   zapatos

Table ventas
For this first exercise, let's assume that the 'ventas' table only allows recording the sale of one product at a time.
id  id_producto
1   1
2   1
3   2
4   2
5   3
6   1

If we look at the sales, we will see that the product with id=1 was sold 3 times, and the product with id=2 was sold 2 times, and the rest have not been sold.
The expected result would be:
nombre         cantidad_ventas
smartphone     3
tablet         2
camiseta       0
zapatos        0

The solution to this exercise requires joining the 'productos' and 'ventas' tables and grouping the results by the product name to finally count the number of sales made.

The important question is: Which type of JOIN should we use for this case? INNER JOIN or LEFT JOIN?

To make the decision, we need to consider whether we want to count products without sales. If we want to count them, then we should use a LEFT JOIN. If we do not want to count them, then we should use an INNER JOIN.
In the example, the report table shows t-shirts and shoes that have not been sold, so we should use a LEFT JOIN.
```sql
SELECT p.nombre, COUNT(v.id) AS cantidad_ventas 
FROM productos p LEFT JOIN ventas v 
ON p.id = v.id_producto 
GROUP BY p.nombre;
```
Exercise
We have a table of users and a table of notes.
We want to make a query that shows the number of notes each user has recorded, even if they have not recorded any notes.
The columns of the tables should be 'email' and 'cantidad_notas'.
Sort the result by the number of notes.
```sql
SELECT u.email, COUNT(n.notas) AS cantidad_notas 
FROM usuarios u LEFT JOIN notas n 
ON u.email = n.email 
GROUP BY u.email;
```
## Normalization
### Introduction to the first normal form
Key ideas
Redundancy in a database can lead to inconsistencies when identifying the correct data, updating it, or deleting it.
Normalization is a process of eliminating redundancy in a database.
What is normalization?
Normalization is a process of eliminating redundancy in a database to prevent inconsistencies.
This process consists of various stages that, when applied correctly, make the database more efficient and easier to maintain.
Each of these stages is called a normal form.

First normal form
In the first normal form, each record should be uniquely identifiable in a table.
Let's look at an example of a table that does not comply with the first normal form:

Nombre   Apellido        Estado_Civil
Juan     Pérez           Casado
María    González        Soltera
Pedro    Rodríguez       Casado
Juan     Pérez           Soltero
María    González        Viuda
Pedro    Rodríguez       Soltero
Juan     Pérez           Divorciado
María    González        Soltera
Pedro    Rodríguez       Casado

In this table, if we ask for the marital status of Juan Pérez, we would not know which one is correct.
Furthermore, is Juan Pérez one person, or are they different people who happen to have the same name and surname?
This is one of the problems that the first normal form aims to solve.

Exercise
Suppose each combination of name and surname in the 'personas' table corresponds to a unique person, and the last record for each person is correct.
Delete the incorrect records. There are ways to do this automatically, but you can simply delete the incorrect records one by one, separating the queries with a semicolon.
It's a tedious task, but you won't forget the lesson of having tables that comply with the first normal form.
This is one way assuming we have incremental IDs, if not, it can be done one by one.
```sql
DELETE FROM personas 
WHERE ID NOT IN (
    SELECT MAX(ID) 
    FROM personas 
    GROUP BY nombre, apellido
);
```
### Converting to 1NF
In the first normal form, there should be a primary key that uniquely identifies each record in a table.
In this case, let's assume that each combination of name and surname in the 'personas' table corresponds to a unique person.
Therefore, Juan Pérez with marital statuses Casado, Soltero, and Divorciado are different people.
To convert this table to the first normal form, we will add a primary key that uniquely identifies each record.
In SQLite, we cannot add a primary key to a table that already has data, so we will have to create a new table with the primary key and then insert the data from the original table into the new table.
```sql
CREATE TABLE personas2 (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    nombre TEXT NOT NULL,
    apellido TEXT NOT NULL,
    estado_civil TEXT NOT NULL
);

/* Select the data from 'personas' and insert it into 'personas2' */
INSERT INTO personas2 (nombre, apellido, estado_civil)
SELECT nombre, apellido, estado_civil 
FROM personas;

/* Drop the 'personas' table */
DROP TABLE personas;

/* Rename the 'personas2' table to 'personas' */
ALTER TABLE personas2 RENAME TO personas;
```
Exercise:
```sql
CREATE TABLE productos2 (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    producto TEXT NOT NULL,
    categoria TEXT NOT NULL,
    precio DOUBLE NOT NULL
);

INSERT INTO productos2 (producto, categoria, precio)
SELECT producto, categoria, precio 
FROM productos;

DROP TABLE productos;

ALTER TABLE productos2 RENAME TO productos;
```
### Repetitive groups
Repetitive groups are an indication that a table does not comply with the first normal form.
What are repetitive groups?
Repetitive groups are a set of fields that repeat in a table.
For example, if a table has fields telefono1, telefono2, and telefono3, these fields could be considered a repetitive group.

id  nombre           apellido        telefono1  telefono2  telefono3
1   Juan             Pérez           12345678   87654321   45678912
2   María            González        23456789   98765432   56789123

Various problems are associated with repetitive groups, but in this specific case, there are two obvious problems:
What if a person has more than three phones? Should the table be modified to add a telefono4, telefono5, etc.? What is the LIMIT?
If most people only have one phone, how is the information for telefono2 and telefono3 handled? Are they left blank? Are they assigned a null value? We would probably end up with a large number of empty fields.
Solving the repetitive group problem is an important step in normalizing a database.
In this case, we could create a 'telefonos' table that contains the phone numbers of people and relates to the 'personas' table, resulting in the following structure:

tabla personas:
id  nombre           apellido
1   Juan             Pérez
2   María            González

tabla telefonos:
id_persona  telefono
1           12345678
1           87654321
1           45678912
2           23456789
2           98765432
2           56789123

This way, an arbitrary number of phone numbers can be stored for each person without modifying the structure of the 'personas' table.

Exercise
Given the following table, identify the repetitive groups and propose a solution to normalize the table.

tabla empleados_desnormalizado:
id  nombre           apellido        proyecto1  proyecto2  proyecto3
1   Juan             Pérez           1          2          3

Identify the repetitive groups in the 'empleados_desnormalizado' table.
The repetitive group is the 'proyecto'.

Create the 'empleados' and 'proyectos' tables in the first normal form to store information about employees and projects in a normalized structure.
Insert the information from the 'empleados_desnormalizado' table into the normalized tables.
Create a query that shows the information of employees and the projects they are assigned to in a normalized structure.
The columns of the query should be 'nombre', 'apellido', and 'id_proyecto'.
```sql
CREATE TABLE empleados (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    nombre TEXT NOT NULL,
    apellido TEXT NOT NULL
);

CREATE TABLE proyectos (
    id_proyecto INTEGER PRIMARY KEY,
    id_empleado INTEGER,
    FOREIGN KEY (id_empleado) REFERENCES empleados (id)
);

INSERT INTO empleados (nombre, apellido) 
VALUES ('Juan', 'Pérez');

INSERT INTO proyectos (id_proyecto, id_empleado) 
VALUES (1,1), (2,1), (3,1);

SELECT e.nombre, e.apellido, p.id_proyecto 
FROM empleados e 
JOIN proyectos p 
ON e.id = p.id_empleado;
```
### Repetitive groups part 2
There are more than one way to have repetitive groups in a table.

Repetitive groups
Repetitive groups are sets of fields that repeat within a table.
However, there are several ways in which these repetitive groups can appear in a table.

In the previous exercise, we saw an example of a repetitive group where there were several fields with the same purpose.
For example: telefono1, telefono2, and telefono3.
Another way to have repetitive groups is to have multiple values in a single field.

The problems associated with this form of repetitive groups are different:
When modifying a person's phone number, the 'Telefonos' field, which has multiple records, must be modified, and we could forget one or accidentally overwrite it.
When the data is stored this way, it is difficult to know how many phones each person has.
The phones could have different formats, and it would be difficult to establish a restriction to ensure all phones have the same format.
Checking if the same phone is assigned to more than one person also becomes more complicated than having a denormalized table.

For example, if we wanted to count the number of phones each person has, we could count the number of '+' symbols in the 'Telefonos' field.
To achieve this, we can calculate the total number of characters in the 'Telefonos' field and subtract the number of characters in the same field after removing the '+' symbols.
```sql
SELECT id, nombre_completo, 
LENGTH(telefonos) - LENGTH(REPLACE(telefonos, '+', '')) AS cantidad_telefonos 
FROM personas;
```
As observed, it is much more complicated than simply counting the records in a table.

Exercise
The exercise is solved as follows because there are no blank products, but if there were, the error would be incorrect because I am adding 1 at the end of the count.
```sql
SELECT id, productos, 
LENGTH(productos) - LENGTH(REPLACE(productos, ' ', '')) + 1 
FROM pedidos;
```
If you want to validate when the 'productos' field has some blank field, you could use the following query.
```sql
SELECT id, productos, 
CASE 
    WHEN LENGTH(productos) = 0 THEN 0 
    ELSE LENGTH(productos) - LENGTH(REPLACE(productos, ' ', '')) + 1 
END AS cantidad_productos 
FROM pedidos;
```
### Repetitive groups part 3
Key ideas
Repetitive groups are an indication that a table does not comply with the first normal form.
There are repetitive groups when you have columns that repeat in a table, for example: proyecto1, proyecto2, proyecto3.
There are repetitive groups when you have multiple values in a single field, for example: producto1 producto2 producto3.
Finally, there are repetitive groups when you have columns that repeat in multiple rows, for example: nombre, apellido.

Let's look at the 'departamentos' table:

nombre_departamento    nombre_persona      apellido_persona
Recursos Humanos       Ana                 Pérez
TI                     Pedro               Rodríguez
TI                     Luis                González
Marketing              Marta               López
Marketing              Elena               Pérez

The previous table, although a bit harder to see, also has repetitive groups.
Given that 'nombre_departamento' repeats in multiple rows for different people.

For example, this could be represented as:

nombre_departamento    personas
Recursos Humanos       Ana Pérez
TI                     Pedro Rodríguez, Luis González
Marketing              Marta López, Elena Pérez

Or as:

nombre_departamento    Nombre Persona 1    Nombre Persona 2    Nombre Persona 3
Recursos Humanos       Ana
TI                     Pedro               Luis
Marketing              Marta               Elena

The problems associated with repetitive groups are similar to those presented in previous exercises.
If you want to change the name of a department, you would have to change it in all rows corresponding to that department.
Or a department could be written in different ways, for example, TI, T.I., Tecnologías de la Información, etc.
And it would be difficult to establish a restriction to ensure all departments have the same name.
To eliminate repetitive groups from this table, a new 'Personas' table should be created, separate from the 'Departamentos' table, that relates people to departments.

Departamentos:
id  nombre_departamento
1   Recursos Humanos
2   TI
3   Marketing

Personas:
id  nombre_persona      apellido_persona    departamento_id
1   Ana                Pérez               1
2   Pedro              Rodríguez           2
3   Luis               González            2
4   Marta              López               3
5   Elena              Pérez               3

Didactic Exercise
With the normalized 'Departamentos' and 'Personas' tables.
Show all departments and how many people work in each one.
The columns should be named 'nombre_departamento' and 'cantidad_personas'.
```sql
SELECT d.nombre_departamento, COUNT(p.id) AS cantidad_personas 
FROM departamentos d 
JOIN personas p 
ON d.id = p.departamento_id 
GROUP BY nombre_departamento;
```
### Partial dependencies
In the first normal form, there must be a primary key that uniquely identifies each record in a table.
In the second normal form, there should be no partial dependencies, meaning each column in a table must depend on the entire primary key and not just part of it.

Partial dependencies
A partial dependency is when a column depends on only part of the primary key.
This, of course, only applies to tables with composite primary keys.

Let's look at an example of a table that has partial dependencies:
We have a grade registration system stored in a single table called 'notas'. The table has the following structure:

ID de estudiante   ID de curso   Nombre del estudiante   Nombre del curso   Nota
1                  101           Juan                   Matemáticas         90
1                  102           Juan                   Historia            85
2                  101           Ana                    Matemáticas         95
2                  103           Ana                    Ciencia             88

In this table, we have a composite primary key consisting of 'ID de estudiante' and 'ID de curso'.
This combination of columns uniquely identifies each row, given that, for this example, a student cannot be enrolled in the same course twice.

Partial dependencies occur when a field like 'Nombre del estudiante' depends only on 'ID de estudiante' and not on 'ID de curso'.
In other words, it depends on part of the primary key and not the whole primary key, hence it is called a partial dependency.

Why is it important to eliminate partial dependencies?
Partial dependencies can cause update and deletion problems.
For example, if a student's name changes, it would have to be changed in all rows corresponding to that student.
Or if a student is deleted, the information about the grades associated with that student would be lost.

Eliminating partial dependencies
To reach the second normal form (2NF), all partial dependencies must be identified.
'Nombre del estudiante' is partial because it depends only on 'ID de estudiante' and not on the whole primary key.
'Nombre del curso' is partial because it depends only on 'ID de curso' and not on the whole primary key.
The next step is to separate the 'notas' table into two additional tables, one for students and one for courses.

Table estudiantes:
ID_de_estudiante   Nombre_del_estudiante
1                  Juan
2                  Ana

Table cursos:
ID_de_curso   Nombre_del_curso
101           Matemáticas
102           Historia
103           Ciencia

Then, the columns 'Nombre del estudiante' and 'Nombre del curso' should be removed from the 'notas' table, and the foreign keys 'ID de estudiante' and 'ID de curso' should be added respectively.

Table notas:
ID_de_estudiante   ID_de_curso   Nota
1                  101           90
1                  102           85
2                  101           95
2                  103           88

Exercise:
Given the normalized 'estudiantes', 'cursos', and 'notas' tables, create a query that shows the student's name, the course name, and the grade each student obtained in each course.
The columns should be named 'nombre_estudiante', 'nombre_curso', and 'nota'.
```sql
SELECT e.nombre_del_estudiante, c.nombre_del_curso, n.nota 
FROM estudiantes e 
JOIN notas n 
ON e.id_de_estudiante = n.id_de_estudiante 
JOIN cursos c 
ON n.id_de_curso = c.id_de_curso;
```
### Transitive dependencies
In the third normal form, there should be no transitive dependencies, meaning a column cannot depend on another column that is not the primary key.

What are transitive dependencies?
Transitive dependencies occur when a column depends on another column that is not the primary key indirectly.
In other words, if column A depends on column B and column B depends on the primary key, then column A depends transitively on the primary key.

For example, consider the following 'Peliculas' table:
ID   Película                 Director                Nacionalidad Director
1    El Padrino              Francis Ford Coppola     Estadounidense
2    Ciudad de Dios          Fernando Meirelles       Brasileño
3    El Laberinto del Fauno  Guillermo del Toro       Mexicano
4    Amélie                  Jean-Pierre Jeunet       Francés
5    Parasite                Bong Joon-ho             Coreano

In this table, we can see that the 'Nacionalidad Director' column depends on the 'Director' column.
In turn, the 'Director' depends on the primary key 'ID'.
Therefore, 'Nacionalidad Director' indirectly depends on the primary key 'ID', implying a transitive dependency.

This type of dependency is important to identify and manage, especially in the context of database normalization, to avoid redundancies and ensure data integrity.

Exercise
Based on the following table in 2NF, called 'Músicos', identify the transitive dependencies and propose a solution to normalize it:

id   pais            musico           edad_musico
1    Estados Unidos  Beyoncé          42
2    Brasil          Gilberto Gil     81
3    Francia         David Guetta     56
4    India           A. R. Rahman     57
5    Corea del Sur   RM               29

The normalized tables should be 'paises' and 'musicos'.
Keep the names of the musicians and countries in the 'musicos' table.
The primary keys should be named 'id' in both tables, and the foreign key should be named 'pais_id'.

Enter the normalized data into the corresponding tables.
```sql
CREATE TABLE paises (
    id INTEGER PRIMARY KEY,
    pais TEXT
);

INSERT INTO paises (id, pais) 
VALUES 
(1, 'Estados Unidos'), 
(2, 'Brasil'), 
(3, 'Francia'), 
(4, 'India'), 
(5, 'Corea del Sur');

CREATE TABLE musicos (
    id INTEGER PRIMARY KEY,
    musico TEXT,
    edad_musico INTEGER,
    pais_id INTEGER,
    FOREIGN KEY (pais_id) REFERENCES paises (id)
);

INSERT INTO musicos (id, musico, edad_musico, pais_id) 
VALUES 
(1, 'Beyoncé', 42, 1), 
(2, 'Gilberto Gil', 81, 2), 
(3, 'David Guetta', 56, 3), 
(4, 'A. R. Rahman', 57, 4), 
(5, 'RM', 29, 5);

SELECT musicos.id, paises.pais 
FROM paises 
JOIN musicos 
ON paises.id = musicos.pais_id;
```
