[Home](index.md)

--limit and order by
SELECT * FROM table WHERE field > 10 ORDER BY field2 LIMIT 5

--Transforming a string to uppercase/lowercase: does not modify data in the table, only transforms it for the query
select upper(email) as email_upper from users;
select lower(email) as email_lower from users;

--Removing whitespace from a string
select trim(name), trim(email) from users;

--Combining functions
select lower(trim(name)) as clean_name, lower(trim(lastname)) as clean_lastname, lower(trim(email)) as clean_email from users;

--Getting the length of a string
select length(lastname) from users;
select lastname, length(lastname) from users;

--Getting the longest name in the table
For example, if we want to select the length of the shortest name in the users table, the query would be:

SELECT LENGTH(name) as name_length FROM users ORDER BY LENGTH(name) LIMIT 1 ;

On the other hand, if we want to get the length of the longest name, we will reverse the order of the selection.

SELECT LENGTH(name) as name_length FROM users ORDER BY LENGTH(name) DESC LIMIT 1 ;

select length(email) from users order by length(email) desc limit 3;

--Ordering all data and the function
we are asked for the longest email instead of just its length
SELECT email FROM users ORDER BY LENGTH(email) DESC LIMIT 1;

Additionally, we may be asked to select all fields of the user whose email is the longest. To achieve this, we can use the following query:
SELECT * FROM users ORDER BY LENGTH(email) DESC LIMIT 1;

Also, they may require us to select all fields of the table and additionally include the length of the email. The idea is similar, we simply add the function to the SELECT:
SELECT *, LENGTH(email) as email_length FROM users ORDER BY LENGTH(email) DESC LIMIT 1;

select email, length(email) from users order by length(email) desc limit 3;

--Concatenating strings
SELECT name || ' ' || lastname AS full_name FROM employees;

select product || '-' || brand as brand_product from products;

--Selecting characters from a string with SUBSTR
SUBSTR( string, start, length )
In the syntax, we can see that the function has 3 arguments. 1. String: the name of the column or word that will be used 2. Start: an integer that specifies the starting position from the[...]

For example, if we have a products table with the field 'name' and want to select only the first letter of each name, we can use the following query:
SELECT SUBSTR(name, 1, 1) AS first_letter FROM products;
to achieve the same goal in PostgreSQL, we should use the LEFT() function

select substr(lastname, 1, 3) as first_letters from users;
select substr(lastname, 2, 3) as three_characters_of_lastname from users
where name like 'Mar%';

--Getting today's date
DATE() we can get today's date.
In MySQL, CURDATE() is used, and in Microsoft SQL Server, GETDATE() is used. When looking for documentation, it is important to specify which engine is being used. In this interactive tutorial we are[...].

SELECT * FROM users WHERE registration_date = DATE(); -- both bring information about today's date
SELECT * FROM users WHERE registration_date = DATE('now');

Select description from tasks where deadline = DATE();

--Getting tomorrow's date
In SQL, it is possible to add dates to get future dates. In SQLite we can achieve this by passing a second argument to the DATE function
DATE('now', '1 day')
In this example, we are adding 1 day to today's date (now). If we want to add more days, for example, 5 days, we will use DATE('now', '5 day').
It is also possible to add weeks and months with:
2 Weeks: DATE('now', '2 week') 3 Months: DATE('now', '3 month')
In a query, this would look as follows:
SELECT * FROM table where date > DATE('now', '2 week')

Select * from tasks where deadline = DATE('now','1 day');

--Getting yesterday's date
Just as it is possible to add dates, it is also possible to subtract them:
DATE('now', '-1 day') DATE('now', '-1 week')
It is important to clarify that when we do not specify the sign, it is assumed to be positive, meaning that
DATE('now', '1 day')
is the same as
DATE('now', '+1 day')

Select amount from earnings where date = DATE('now','-1 day');

--Extracting the year
We are asked to show all the information in the table and additionally add a column with the year of the sale.
SELECT *, strftime('%Y', sale_date) as sale_year FROM sales
To display the results of this type of function, it is necessary to assign a name to the new column, otherwise, the resulting column will retain the name "strftime('%Y', sale_d[...].

select amount, strftime('%Y',sale_date) as sale_year from sales;

--Extracting the month
SELECT strftime('%m', column) FROM table
In this case, to get the month, we pass %m as an argument to the strftime function.

Select amount, strftime('%m',sale_date) as sale_month, strftime('%Y',sale_date) as sale_year from sales;

--Extracting both the month and year
We have learned to extract the month and the year from a date. However, how could we extract both data in a single column?
To extract both the month and the year from a date in a single column, you can use the function strftime('%m-%Y'). This will allow you to get a result in the format "month-year".

select amount, strftime('%m-%Y',sale_date) as month_year from sales;

--Extractions and where
We are asked to show all sales from the year 2012. For this, we will use the strftime function to extract the year from the dates, and then filter by the indicated year:
SELECT * FROM sales WHERE strftime('%Y', sale_date) = '2012';

Select * from sales where strftime('%Y',sale_date) = '2015';

--Aggregation functions
--The highest value in a column
the function MAX() which allows us to find the highest value in the specified field.
SELECT MAX(column) FROM table
We can find the highest salary using:
SELECT MAX(salary) FROM employees;
When we use aggregation functions, we cannot directly select other items from the same table. For example, SELECT email, MAX(salary) FROM employees; would result in an error because we would be[...]

select MAX(age) from employees;

--The lowest value in a column
. This function takes as an argument the name of the column and returns the smallest value in that column.
SELECT MIN(column) FROM table

select MIN(salary) from employees;

--Sum of elements in a column
SUM(). With this, we can sum all the elements of a column.
SELECT SUM(column) FROM table
It is important to note that the column on which the SUM() function is applied must contain numerical values, otherwise, the query may generate an error or an unexpected result.

select sum(salary) from employees;

--Average of a column
AVG(). The name of the function comes from the English term average
SELECT AVG(column) FROM table

select AVG(salary) from employees;

--Counting elements in a table
COUNT(). With this, we can count the number of records in a table.
SELECT COUNT(*) FROM table

select count(*) from employees;

--Exercise 1: Aggregation functions with where
SELECT AVG(column1) FROM table WHERE column2 < value
select sum(salary) from employees where age > '27'

--Exercise 2: Aggregation functions with where
select AVG(salary) from employees where salary > '50000'

--Exercise 3
select count(*) from employees where department = 'Marketing'

--Exercise 4:
And is exclusive. when you want to filter by multiple values in a column, you must use OR instead of AND

select count(*) from employees 
where department = 'Finance'
or department = 'Marketing';

--Counting with string conditions
select count(*) from users where name like'%a'

--Distinct
--Selecting filtering repeated data
In SQL, the DISTINCT keyword allows us to filter repeated results from a query.
SELECT DISTINCT color AS unique_color
FROM colors

select distinct color as unique_color from colors;

--Selecting unique emails
select distinct email as unique_email from users;

--Selecting distinct years
We are asked to create a query that shows the years in which transactions have been made, excluding repetitions.
As we learned in previous exercises, to get the year from the sale date, we can use the following code:
SELECT strftime('%Y', sale_date) as sale_year FROM sales
However, to ensure we obtain unique years, we can add the DISTINCT clause to our query as follows:
SELECT DISTINCT strftime('%Y', sale_date) as unique_year FROM sales

select distinct strftime('%m', sale_date) as unique_month from sales;

--Counting distinct values
If we want to count the distinct values in a column of a table, we can combine the COUNT and DISTINCT functions as follows:COUNT(DISTINCT column)

select count(distinct phone) as unique_phones from users;

--Counting unique emails
select count(distinct email) as email_count from users;

--Distinct with multiple columns
We can use DISTINCT with more than one column to obtain unique combinations of those columns. Suppose you have a table called employees with the columns department and position.
We can then get all the unique combinations of Department and Position using the following query:
SELECT DISTINCT department, position FROM employees;

select distinct category, price from products;

--Introduction to Groups
--Grouping values with GROUP BY
The GROUP BY clause is a powerful tool in SQL that is used to group rows with identical values in one or more specific columns, allowing for aggregation operations[...]
In this first exercise, we will learn to use GROUP BY to get all the distinct elements of a table, the same as we previously did with distinct.

We can select unique elements using GROUP BY as follows:
SELECT color as unique_color FROM colors GROUP BY color

select email as unique_email from users group by email;

--Grouping and counting
GROUP BY is commonly used together with aggregation functions such as COUNT, MAX, MIN, SUM, and AVG to obtain summarized information from a dataset.
We want to know how many times each color appears. We can achieve this by combining GROUP BY and the aggregation function COUNT
SELECT color, COUNT(color) as Repetitions FROM colors GROUP BY color

Select email, count(email) as repetitions from users group by email;

--Practicing grouping and counting
Select department, count(department) as employee_count from employees group by department;

--Grouping and summing
If we want to calculate how much each customer has spent, we can perform the following query
SELECT Customer, SUM(Amount) AS Total_Amount FROM orders GROUP BY Customer;

Select category, SUM(amount) as total_amount from sales group by category;

--Grouping and averaging
calculating averages by group.
SELECT group, AVG(column) FROM table GROUP by group

Select full_name, AVG(grade) as Grade_Average from students group by full_name;

--Maximum by group
getting the highest amount in each group. The syntax of the query will be the same as seen previously, i.e.:
SELECT group, MAX(column) FROM table GROUP by group

select category, MAX(amount) as highest_amount from sales group by category;

--Minimum by group
getting the lowest amount in each group. The syntax of the query will be the same as seen previously, i.e.:
SELECT group, MIN(column) FROM table GROUP by group

Select category, MIN(amount) as lowest_amount from sales group by category;

--Aggregation functions and dates
When building reports, we frequently need to deliver information grouped by a period of time. To achieve this, we will use a combination of GROUP BY with the strftim[...]
We are asked to determine the total amount of sales per year. To solve this, we have to group by date and sum the amounts as follows:
SELECT SUM(amount), strftime("%Y", sale_date) AS year FROM sales GROUP BY strftime("%Y", sale_date)

Select sum(amount) as sale_sum, strftime('%m', sale_date) as month from sales group by strftime('%m', sale_date);

select strftime('%m',registration_date) as month, count(registration_date) as user_count from registrations group by strftime('%m', registration_date);

--Grouping without specifying column names
When it comes to grouping data in an SQL query, there is a way to avoid the redundancy of the SELECT clause. For example, consider the following query:
SELECT strftime("%Y", sale_date) AS year, SUM(amount) FROM sales GROUP BY strftime("%Y", sale_date)
You can simplify it as follows:
SELECT strftime("%Y", sale_date) AS year, SUM(amount) FROM sales GROUP BY 1
This notation is interpreted as "group by the first criterion". It is also possible to apply this syntax in the ORDER BY clause:
SELECT strftime("%Y", sale_date) AS year, SUM(amount) FROM sales GROUP BY 1 ORDER BY 1
This way, you can achieve the same grouping and ordering without repeating the SELECT clause expression.

select email, count(email) as repetitions from users group by 1 order by 1;

--Grouping by multiple columns
In SQL, it is possible to group by multiple columns using the following syntax:
SELECT column1, column2, aggregation_function(column3) FROM table GROUP BY column1, column2
And as we learned in the previous exercise, we can also write the query as follows:
SELECT column1, column2, aggregation_function(column3) FROM table GROUP BY 1, 2

Calculate the average of each student in each subject. The columns should be named email, subject, and grade_average
select email, subject, avg(grade) as grade_average from students group by 1,2;

-- Introduction to Having
In SQL, the GROUP BY clause allows us to group data. If we want to filter the obtained information, we will use HAVING.
HAVING is used to filter the results of a query that involves aggregated functions. In other words, HAVING allows applying filtering conditions to the results of functions like COUNT[...]
We are asked to create a report showing the months and the number of enrollees, but only where there are 2 or more enrollees.

SELECT strftime("%m", Registration_Date) AS month, COUNT(Registration_Date) user_count 
FROM registrations 
GROUP BY strftime("%m", Registration_Date)
HAVING user_count >= 2
In this query, we first use GROUP BY to group by month. Then, we use the aggregation function COUNT(Registration_Date) to count the number of enrollees. After grouping[...]

Select strftime('%m', registration_date) as month, count(registration_date) as user_count from registrations
group by strftime('%m', registration_date)
having user_count = 1; 

--Finding duplicates
One of the most common uses of HAVING is finding duplicates.
Show the emails that appear more than once. The result table should have two columns: one named email, and another named email_count that shows the number of repetitions correspond[...]

select email, count(email) as email_count from corporate_emails
group by email
having email_count > 1;

--Having and count
Create a query that shows the number of users and the department where there are more than one employee. The columns should be named user_count and department, respectively.

select count(name) as user_count, department from employees
group by department
having user_count > 1;

--Having and average
Create a query to determine which students passed. The passing criterion is an average grade >= 50.
The columns to show should be email and grade_average.

select email, avg(grades) as grade_average from grades
group by email
having grade_average >= 50;

--Having and order
Once we have grouped data using the GROUP BY clause, it is common to need to order those groups according to some specific criterion. Generally, we want to order the groups by func[...]
The order of the clauses in a query should be as follows:
Order	Clause	Description
1	SELECT	Specifies the columns to be returned in the result.
2	FROM	Specifies the tables from which to extract the data.
3	WHERE	Filters records before any aggregation or grouping.
4	GROUP BY	Groups records by one or more columns.
5	HAVING	Filters records after aggregation.
6	ORDER BY	Orders the returned records by one or more columns.
7	LIMIT	Limits the number of returned records.

Given the following sales table, write an SQL query to obtain the products that have been sold in a total quantity greater than 1000, ordered in descending order of quantity sold.

select product, sum(quantity) as total_quantity from sales
group by product
having total_quantity > 1000 order by 2 desc;

Your task is to write an SQL query that returns the departments whose average salary is greater than 3000, ordered from highest to lowest average salary. The results should show the department name[...]

Select Department, AVG(salary) as Average_Salary from employees
group by Department
having Average_Salary > 3000 order by 2 desc;

--Subqueries
--Introduction to subqueries
Subqueries, also known as "subqueries", allow us to use the results of one query within another query.
We are asked to select all people earning above the average.
These types of questions can be answered using subqueries.
The idea to answer this is as follows:
We calculate the average SELECT avg(salary) FROM employees
We select all employees whose salary is greater than the previous query. SELECT * FROM employees WHERE salary > (SELECT AVG(salary) FROM employees)

select * from employees where salary <= (select avg(salary) from employees)

--Subqueries and where part 1
Within subqueries, we can use the same clauses we have learned so far, such as the WHERE clause. This means we can apply the WHERE clause both inside the sub[...]

Select all the information of the records that are greater than the average salary of the finance department.
Select * from employees where salary > (select avg(salary) from employees where department = 'Finance')

Using the data from the employees table, select all employees whose salary is higher than the employee with the highest salary in the finance department.
Select * from employees where salary > (select max(salary) from employees where Department = 'Finance')

Select all records above the average grade.
Select * from grades where grades > (Select avg(grades) from grades)

--Subqueries with IN
The IN operator is a very useful operator in subqueries
We want to select all the codes from Argentina, Brazil, Chile, or Colombia. One way to approach the problem would be to combine all the options with where and multiple or operators. Another option i[...]
SELECT * 
FROM countries 
WHERE country IN ('Argentina', 'Brazil', 'Chile', 'Colombia')
Similarly, we can make a query like the following:
SELECT *
FROM table
WHERE column IN (SELECT * from another_table)

We are asked to show the names of all people who have an average
