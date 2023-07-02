Links : https://github.com/mobinni/Complete-SQL-Database-Bootcamp-Zero-to-Mastery/blob/master/SQL%20Deep%20Dive/Window%20Functions/exercises.sql

#### Order of operations
from => where => select

#### Operator precedence
() => - / => + & - => not => and => or


## Agregate functions

### Count
```sql
select count(emp_no) from employees;
```
### Max
Get highest salary

```sql
select max(salary) from salaries s;
```

### Sum
Get total amount of salaries paid
```sql
select sum(salary) from salaries s;
```

### EXERCICES
What is the average salary for the company?
```sql
select avg(salary) from salaries;
```

What year was the youngest person born in the company?
```sql
select max(birth_date) from employees;
```

## Select
#### Where clause
```sql
select
first_name ,last_name  
from
employees e
where
first_name = 'Mayumi' and last_name = 'Schueller';
```

#### NOT clause
```sql
select count(age) from customers c
where not age = 55 and not age = 20;
```

#### NULL VALUES

select 1 = 1 		 => true
select 1 != 1 		 => false
select null = null   => null
select null <> null  => null

#### NULL + IS OPERATOR
```sql
select *
from 
    employees e
where
    birth_date = '10/12/1988' is not true;
```

```sql
select *
from
    employees e
where
    last_name is null;
```

#### NULL + COALESCE FUNCTION
similar to If else
```sql
select coalesce (
    last_name,  -- if last_name IS NULL => first_name
    first_name,	-- if first_name IS NULL => default value "No Name"
    'No name'
) as name
from employees;
```

Assuming a students minimum age for the class is 15, what is the average age of a student?
give defeault value of 15 if age is unknown
```sql
select avg(coalesce(age, 15)) from student;
```

Replace all empty first or last names with a default?
```sql
select
    emp_no,
    coalesce(first_name, 'Inconnu') as FirstName,
    coalesce(last_name,'Inconnu') as LastName
from
employees;
```
####  BETWEEN X AND Y
```sql
select *
from
    salaries s
where 
    salary between 45000 and 50000;
```

####  SELECT IN
```sql
select *
from employees e
where 
    emp_no in (10001, 10006, 11008);
```

####  LIKE
WILD CARDS
- % => any number of characters
- _ => 1 character (not optional)


First name start with the letter M

```sql
select *
from employees e
where last_name like '00%';
```

EXAMPLES
- like '%2'  => Fields ends with 2
- like '%2%' => Fields that have 2 anywhere in the value
- like '_00%' => Fields that have 2 zero's as the second and third character + anything after ( Ex: 2000, but not 00)
- like '2_%_%' => Fields that start with "2" + have 1 character + anything + have 1 chatacter + anything ( Ex: 233 or 2555577777, but not 22)
- like '2___3' => Fields that have 5 characters, start with "2" end with "3"

ILIKE => case insensitive matching


####  LIKE + CASTING
CASTING => In postgres you need to cast integers to use like

```sql
select *
from salaries
where salary::text like '6_1_6';
```

####  DATES
UTC = Time Standard

```sql
show timezone;
set TIME zone 'UTC';
```

Change timezone to UTC on all sessions

```sql
alter user postgres set
timezone = 'UTC';
```

- ISO 8601 - Formating DateTime Standard
- FORMAT : YYYY-MM-DDTHH:MM:SS
- TIME   : 2017-08-14T12:47:16+02:00

## Create Table

```sql
create table timezones (
  ts TIMESTAMP without TIME zone,
  tz  TIMESTAMP with TIME zone
)
```

```sql
insert into timezones values(
  timestamp without time zone '2000-01-01 10:00:00-05',
  timestamp with time zone '2000-01-01 10:00:00-05'
)
```

## Date Operators
Get current date
```sql
select now()::date;
select current_date;
```

Format current date
```sql
select to_char(current_date, 'dd/mm/yyyy');
```

Difference betweeen 2 dates  
Return difference in days  
Ex: 8554 days 16:04:46.525787
  
```sql
select now() - '2000/01/01';
```

Cast to date
Ex : String '1800/12/27' => Iso formati 1800-12-27
  
```sql
select date '1800/12/27';
```

#### AGE

Get time till today
https://www.postgresqltutorial.com/postgresql-string-functions/postgresql-to_char/
  
```sql
select age(date '2000/12/10'); -- 22 years 5 mons 24 days
select age(date '1988/12/10', date '1900-01-01'); 				 -- 88 years 11 mons 9 days
select to_char(age(date '1988/12/10', date '1900-01-01'), 'YY'); -- 88 => number of YEARS
select to_char(age(date '1988/12/10', date '1900-01-01'), 'MM'); -- 11 => number of months
select to_char(age(date '1988/12/10', date '1900-01-01'), 'DD'); -- 09 => number of days
select to_char(age(date '1988/12/10', date '1900-01-01'), 'DDD'); -- 32019 => number of days between 2 dates
```


#### EXTRACT

Get Day, month etc from date
  
```sql
select extract (day from date '1988/12/10') as DAY_FROM_DATE;
select extract (month from date '1988/12/10') as MONTH_FROM_DATE;
```

#### DATE_TRUNC
```sql
select date_trunc('year', date '1992/11/15'); --  1992-01-01 01:00:00.000 +0100
```

#### INTERVAL
Can use with Years, Months etc.

```sql
select *
from employees e
where hire_date <= now() - interval '38 years';
```

```sql
select *
from employees e
where hire_date <= now() - interval '38 years 1 months 3 days 3 hours 20 minutes';
```


#### INTERVAL + EXTRACT
6 years (20 months = 1 year + 8 months)
```sql
select
extract (year from interval '5 years 20 months');
```

### EXERCICES DATE FILTERING

#### Get me all the employees above 60, use the appropriate date functions

```sql
SELECT to_char(age(birth_date), 'YY') as years_old, * FROM employees
where (to_char(age(birth_date), 'YY'))::int > 60;
```

```sql
select extract (year from age(birth_date)) as years_old, * from employees
where extract (year from age(birth_date)) > 60;
```

#### Who are the employees where hired in February?

```sql
select extract (month from hire_date) as month_of_hire, * from employees
where extract (month from hire_date) = 2 and extract (year from hire_date) = extract(year from current_date);
```

#### Who many where hired in February (all years included)?

```sql
select count(emp_no) from employees
where extract (month from hire_date) = 2;
```

#### Who is the oldest employee?
```sql
select max(age(birth_date)) from employees;
```

#### How many orders were made in January 2004
```sql
select count(orderid) from orders where
date_trunc('month', orderdate) = date '2004/01/01';
```


## ORDER BY + Expression

#### Order by length
```sql
select first_name, last_name from employees
order by length(first_name) desc;
```

## JOINS

#### USING instead JOIN ON
```sql
select e.emp_no, e.first_name, d.dept_name
from employees e
inner join dept_emp de using(emp_no)
inner join departments d using(dept_no)
```

## GROUP BY
```sql
select d.dept_name, COUNT(e.emp_no) as "# of employees"
from employees e
inner join dept_emp de on de.emp_no = e.emp_no
inner join departments d on de.dept_no = d.dept_no
group by d.dept_no;
```

## HAVING
Having applies to the group results, or group columns

```sql
select 
    d.dept_name, 
    COUNT(e.emp_no) as "# of employees"
from employees e
inner join dept_emp de on de.emp_no = e.emp_no
inner join departvments d on de.dept_no = d.dept_no
where 
    e.gender = 'F'
group by d.dept_no
having COUNT(e.emp_no) > 25000
order by COUNT(e.emp_no)
```

## UNION and UNION ALL
#### union all doesn't remove duplicates
name "prod_id" is necessary for the two combined tables

```sql
select null as "prod_id", sum(ol.quantity)
from orderlines as ol

union

select prod_id as "prod_id", sum(ol.quantity)
from orderlines as ol
group by prod_id
order by prod_id desc;
```

## GROUPING SETS
```sql
select 
    prod_id as "prod_id",
    orderlineid,
    sum(ol.quantity)
from 
    orderlines as ol
group by
grouping sets (
    (),
    (prod_id),
    (orderlineid)
)
order by prod_id desc, orderlineid desc;
```

## ROLL UP
#### all possible combinantions of grouping sets
```sql
select 
    extract (year from orderdate) as 'year'
    extract (month from orderdate) as 'month'
    extract (day from orderdate) as 'day',
    sum(ol.quantity)
from orderlines as ol
group by
rollup (
extract (year from orderdate),
extract (month from orderdate),
extract (day from orderdate)
)
order by
extract (year from orderdate),
extract (month from orderdate),
extract (day from orderdate)
```

## OVER = WINDOW FUNCTION
Get a max salary column for each row  
Applies to each row, low performance
```sql
select
    *,
    max(salary) over()
from salaries;
```

### Partition = used like a group by
Get department average salary

```sql
select 
    *,
    avg(salary) over(
        partition by d.dept_name -- like a group by
    )
from salaries
join dept_emp de using (emp_no)
join departments d using (dept_no);
```

#### ORDER BY
Count applied makes the col count the rows cumulative of the partition before + current
```sql
select 
    emp_no, 
    count(salary) over (
        order by emp_no 		
    )
from salaries s;
```

#### FRAMES
Count applied only to the partition (not cumulative)
```sql
select
    emp_no,
    salary,
    count(salary) over (
        partition by emp_no
        order by salary
    )
from salaries s;
```

#### ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
Works like an order by salary with no partition
```sql
select
    emp_no,
    salary,
    count(salary) over (
        partition by emp_no
        ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
    )
from salaries s;
```

#### Get current salary of employee
LAST_VALUE() (window function)  
Returns the last value in an ordered partition of a result set.  
Range between unbounded preceding and unbounded following === du premier au dernier element de la partition

```sql
select
    distinct e.emp_no,
    LAST_VALUE(s.salary) over(
        partition by e.emp_no
        order by s.from_date
        range between unbounded preceding and unbounded following
    ) as current_salary
from salaries s
join employees e using (emp_no)
join dept_emp de using (emp_no)
join departments d using (dept_no);
```

#### FIRST_VALUE (window function)
I want to know how my price compares to the item with the loweest price in the same category

```sql
select
    prod_id,
    price,
    category,
    first_value (price) over(
        partition by category order by price
        range between unbounded preceding and unbounded following
    ) as "cheapest_category"
from products
```

OR SIMPLER
```sql
select
    prod_id,
    price,
    category,
    min (price) over(partition by category) as "cheapest_category"
from products
```

#### LAST_VALUE (window function)
I want to know how my price compares to the item with the highest price in the same category

```sql
select
    prod_id,
    price,
    category,
    last_value  (price) over(
        partition by category order by price
        range between unbounded preceding and unbounded following
    ) as "most_expensive_item_in_category"
from products;
```

OR SIMPLER
```sql
select
    prod_id,
    price,
    category,
    max (price) over(partition by category) as "most_expensive_item_in_category"
from products;
```

#### SUM (window function)
I want to see how much cumulatively a customer has ordered at our store
```sql
select
    o.orderid,
    o.customerid,
    o.netamount,
    sum(o.netamount) over(
        partition by o.customerid
        order by o.orderid  
    ) as custumer_sum
from orders o
order by o.customerid;
```

#### ROW_NUMBER
Takes number of the current row within the partition starting from 1 regardless of framing  
I want to know where my product is positioned in the category by price
```sql
select
    prod_id,
    price,
    category,
    row_number() over(partition by category order by price) as "position in category by price"
from products;
```

#### Show the population per continent
```sql
select  
    distinct continent,
    sum(population) over(
        partition by continent
    ) as population_by_continent
from country;
```

```sql
select  
    distinct continent,
    sum(population) over w1 as population_by_continent
from country
window w1 as (partition by continent);
```


#### Calculate the percentage of the world population, divide the population of that continent by the total population
```sql
select  
    distinct continent,
    sum(population) over w1 as population_by_continent,
    CONCAT(
    ROUND(
        (
            sum(population::float4) over w1 /
            sum(population::float4) over()
        ) * 100
    ), '%') as "percentage of population"
from country
window w1 as (partition by continent);
```

#### Count the number of towns per region
```sql
SELECT
    distinct r.id,
    r.name,
    COUNT(t.id) over (partition by r.id order by r.name)
FROM regions r
inner join departments d on d.region = r.code
inner join towns t ON t.department = d.code
order by r.id;
```

## CONDITIONALS
#### CASE in SELECT
```sql
select
    o.orderid,
    o.customerid,
case
    when o.customerid = 1
    then 'my first customer'
    else 'not my first customer'
    end
from orders
order by o.customerid
```

#### CASE in WHERE
```sql
select
    o.orderid,
    o.customerid,
    o.netamount
from orders o
where case
    when o.customerid > 10
    then o.netamount < 10
    else o.netamount > 100
    end
order by o.customerid
```

#### CASE in aggregate functions
```sql
select
SUM(
    case when o.netamount < 100
    then -100
    else o.netamount
    end
) as returns,
SUM(o.netamount) as "normal total"
from orders o
```


## NULLIF
* If value1 = value2      => return null  
* NULLIF(0, 0);           => null  
* NULLIF('ABC', 'DEF');   => ABC  

```sql
select NULLIF('ABC', 'DEF');
```

## VIEWS
Views are output of the query we ran
- not materialized  
      - query gets re-run each time the view is called on, storing a reference to the query

- materialized  
      - stores the data physically and periodically updated it when tables change


#### Create view
```sql
create view view_name as query_name
```

#### Update view
```sql
create or replace view view_name as query_name
```

#### Rename view
```sql
alter view view_name rename to new_view_name
```

#### Delete
```sql
drop view [if exists] view_name
```

#### Get the most recent salary of an employee

```sql
create or replace view last_salary_change as
select  
    e.emp_no,
    max(s.from_date)
from salaries s
join employees e using(emp_no)
join dept_emp de using(emp_no)
join departments d using(dept_no)
group by e.emp_no
order by e.emp_no
```

```sql
select * from salaries s
join last_salary_change l using(emp_no)
where l.max = s.from_date
order by emp_no;
```

```sql
select
    s.emp_no,
    d.dept_name,
    s.from_date,
    s.salary
from last_salary_change
join salaries s using(emp_no)
join dept_emp de using(emp_no)
join departments d using(dept_no)
where max = s.from_date
```

## INDEXES
When to use :
* Index foreign keye
* Index primary keys and unique columns(not updated often, columns that can only get certain inserts)
* Index on columns that end up often in the order by / where clause
  

 When NOT to use :
* On small tables
* On tables that are updated frequently
* On columns that can contain null values
* On columns that have large values (descriptions, reviews)
  
#### Create Index
```sql
create unique index index_name on table_name (co1, col2, ...)
```

#### Delete Index
```sql
drop index index_name
```

#### Partial index
expressin salaries > 5000
```sql
create index index_name on table_name (<expression>)
```

#### normal index
```sql
create index idx_countrycode on city (countrycode);
```

#### Partial index
```sql
create index idx_countrycode_partial on city (countrycode) where countrycode in ('TUN', 'BE', 'NL');
```

#### explain, check perf
```sql
explain analyze
select "name", district, countrycode from city
where countrycode in ('TUN', 'BE', 'NL');
```

#### Postgresql type of indexes

Depending on the type of condition you use index can speed x10 your query  

* B-tree 
  - default 
  - for operations : < > = between, in, is null, is not null
* Hash   
  - for operations : =
* Gin 
  - Generalized Inverted Index - multiple values stored in a single field
  - for arrays
* Gist 
  - Generalized search tree 
  - geometric data and full-text search


#### Change type index on create
```sql
create index idx_countrycode
on city
using hash (countrycode)
```

## SUBQUERY / Inner Query / Inner Select

### SUBQUERY in WHERE

```sql
select *
from <table>
where <column> <condition> (
    select <col> -- single result only, like average
    from <table>
    [where | group by | order by | ...]
)
```

### SUBQUERY in SELECT

```sql
select (
    select <col>
    from <table>
    [where | group by | order by | ...]
) -- must return a single record, like average
from <table> as <name>
```

```sql
select title, price, (
    select avg(price) from products
) as "global_avg_price"
from products;
```

### SUBQUERY in FROM

```sql
select *
from (
    select <col1>, <col2>
    from <table>
    [where | group by | order by | ...]
) as <name>
```

```sql
select title, price, (
    select avg(price) from products
) as "global_avg_price"
from (
    select * from products where price < 10
) as "products_sub";
```

### SUBQUERY in HAVING

```sql
select *
from <table> as <name>
group by <col>
having ( -- must return single col, like max
    select <col1>
    from <table>
    [where | group by | order by | ...]
) > X
```


#### SUBQUERY TIPS
#### Must be placed on the right side of the operator

```sql
select *
from <table>
where X > (
    select MAX(<col>) -- single result only
    from <table>
    [where | group by | order by | ...]
)
```

#### Use single-row operators with single-row subqueries

```sql
select *
from <table>
where X >= | <= | = | != (
    select MAX(<col>) -- single result only
    from <table>
    [where | group by | order by | ...]
)
```

### SUBQUERY TYPES
* Single row
* Multiple row
* Multiple column
* Correlated
* Nested

#### Single row SUBQUERY 
```sql
select emp_no, salary
from salaries
where salary = (select avg(salary) from salaries)
```

```sql
select emp_no, salary, (select avg(salary) from salaries)
from salaries;
```

#### Multiple row SUBQUERY
```sql
select title, price, category
from products
where category in (
select category from categories
where categoryname in ('Comedy', 'Family', 'Classics')
)
```

#### Multiple column SUBQUERY
```sql
select emp_no, salary, dea.avg as "Department avg salary"
from salaries s
join dept_emp de using(emp_no)
join (
    select dept_no, avg(salary) from salaries s2
    join dept_emp de2 using(emp_no)
    group by dept_no
) as dea using(dept_no)
where salary > dea.avg
```

#### Correlated SUBQUERY
reference one or more columns in the outer statement - RUNS AGAINST EACH ROW
```sql
select emp_no, salary, from_date
from salaries as s
where from_date = (
    select max(s2.from_date) as max
    from salaries as s2
    where s2.emp_no = s.emp_no
)
order by emp_no;
```

### SUBQUERY EXAMPLES

#### show all employees older than the average age
```sql
select first_name, last_name, age(birth_date)
from employees e
where age(birth_date) > (select avg(age(birth_date)) from employees e2);
```

#### show the title by salary for each employee
```sql
select
emp_no,
salary,
from_date,
(select title from titles t where t.emp_no = s.emp_no and t.from_date = s.from_date)
from
salaries s
order by emp_no
```

#### show the most recent employee's salary
var 1 - 9sec (SLOWER)
```sql
select
    emp_no,
    salary as most_recent_salary,
    from_date
from salaries s
where from_date = (
    select max(from_date)
    from salaries s2
    -- correlated subquery
    where s2.emp_no = s.emp_no
) -- runs on each row individually
order by emp_no ASC
```

#### show the most recent employee's salary
var 2 - 3sec (FASTER)
```sql
explain analyze 
select
    emp_no,
    salary as most_recent_salary,
    from_date
from salaries s
join (
    select emp_no, max(from_date) as max -- can get multiple columns in join
    from salaries s2
    group by emp_no
) as ls using (emp_no)
where ls.max = s.from_date
order by emp_no ASC
```

### SUBQUERY OPERATORS

#### EXISTS 
```sql
select firstname, lastname, income
from customers c
where exists (
    select 
        * 
    from orders o
    where 
        c.customerid = o.customerid 
        and totalamount > 400
) and income > 9000
```

#### IN and NOT IN 
#### check if the value is equal to any of the rows in the return (null yields null)
```sql
select *
from products
where category in (
    select category 
    from categories
    where 
        categoryname not in ('Comedy', 'Familiy', 'Classics')
)
```

#### ANY and SOME 
#### check each row against the operator and if any comparison matches return true
```sql
select 
    *
from products
where category = ANY (
    select category from categories
    where categoryname in ('Comedy', 'Familiy', 'Classics')
)
```

#### ALL 
#### check each row against the operator and if all comparisons match return true
#### Get the products where the sales are greater than the average sales of all categories
```sql
select 
    prod_id, 
    title, 
    sales
from products
join inventory as i using (prod_id)
where i.sales > all (
    select avg(sales) from inventory
    join products as p2 using (prod_id)
    group by p2.category
)
```

#### Single value comparison 
#### Subquery must return a single row. Check comparator agains row

```sql
select 
    prod_id
from products
where category = (
    select category
    from categories
    where categoryname in ('Comedy')
)
```

## EXERCICES

#### Get all orders from customers who live in Ohio (OH), New York (NY) or Oregon (OR) state ordered by orderid

#### var 1 : JOIN
```sql
explain analyze 
select 
    o.orderid, 
    c.state 
from orders o
join customers c ON c.customerid = o.customerid
where 
    c.state in ('OH', 'NY', 'OR')
order by o.orderid ;
```

#### var 2 : SUB QUERY
```sql
explain analyze 
SELECT 
    c.firstname, 
    c.lastname, 
    o.orderid
FROM orders AS o, (
    SELECT customerid, state, firstname, lastname
    FROM customers
) AS c
WHERE  
    o.customerid = c.customerid
    AND c.state IN ('NY', 'OH', 'OR')
ORDER BY o.orderid;
```


#### Filter employees who have emp_no 110183 as a manager
#### var 1 : JOIN
```sql
select * from employees e
join dept_emp de ON e.emp_no = de.emp_no
join dept_manager dm on de.dept_no = dm.dept_no
where dm.emp_no = 110183;
```

#### var 2 : SUB QUERY
```sql
select 
    * 
from employee
where emp_no in (
    select emp_no
    from dept_emp
    where dept_no in (
        select dept_no
        from dept_manager
        where emp_no = 110183
    )
);
```





































