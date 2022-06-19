> create DATABASE `create`;
> create schema testdb;
> create schema if not exists testdb; # postgres does not support if not exists 
> postgres only have text type
> postgres does not support enum



'''
create table users (
    full_name varchar(200),
    yearly_salary int,
    current_status enum('employed', 'self-employed', 'unemployed') -- MySQL syntax
);
'''

'''
create type employment_status as enum (
'employed', 'self-employed', 'unemployed'
);
 create table users (
     full_name varchar(200),
     yearly_salary int,
     current_status employment_status 
);

'''


'''
create table employers (
    company_name varchar(250),
    company_address varchar(300),
    yearly_revenue NUMERIC(5,2),
    is_hiring BOOLEAN
)

'''


'''
create table conversation (
    user_name varchar(200),
    employee_name varchar(250),
    message text,
    date_sent TIMESTAMP default current_timestamp
);

'''

'''
alter table employers
    modify column  yearly_revenue float(5,2);  -- mysql syntax, deprecated, just use float
    modify column  yearly_revenue float;  -- mysql syntax
'''


'''
alter table employers
    alter column  yearly_revenue set data type float; -- postgres syntax
'''

'''
alter table users
    alter column full_name set not null; --postgres syntax for set column not null
'''



'''
alter table users
modify  column full_name  varchar(200) not null,
modify  column current_status  enum('employed', 'self-employed', 'unemployed') not null; -- MySQL syntax 
'''

## column check constraint

'''
create table users (
    full_name varchar(200),
    yearly_salary int check ( yearly_salary > 0 and yearly_salary < 100000 or yearly_salary != 200000) null,
    current_status enum('employed', 'self-employed', 'unemployed'), -- MySQL syntax
    check (yearly_salary < max_salary)
);

'''



'''
alter table users add constraint yearly_salary_positive check(yearly_salary > 0);


'''



'''
alter table users
add column id int primary key auto_increment; -- mysql syntax
'''



'''
alter table users
    add column id serial primary key ; -- postgres syntax

'''
> In most SQL databases / environments (e.g, Postgresql), AUTO_INCREMENT is not supported, Use serial primary key  instead of int primary key auto_increment


## Text Encoding and Collation

+ Encoding  determine  Which characters are supported and can be stored and can be set at server,database, table or column levels in mysql, 
+ Collation determine How are characters compared (e.g, for ordering)


## More ways OF creating Tables

+ Temporary Tables
  + CREATE TEMPORARY table ...
  + Tables that are only stored temporaryly(in memory of the database server ,
  + Useful for non-permanent data (e.g. intermediate results)
+ Tables Based On Other Tables / Data
  + CREATE TABLE  .. as <query>
  + Creates a table and pre-populated it with data from a query result set
  + Useful if a subset of data from another table should be stored in a separate table



'''
create table films_recent as
    select * from films where date_prod >= '2012-01-01';
'''

## Generated Columns


> alter table users add column full_name varchar(400) generated always as (concat(first_name,' ', last_name));  -- mysql syntax

> select * from sales where extract(day from date_fullfilled - date_created); -- for timestamp type columns 

> create view  view_name as select query


## Data Normalization

+ 1NF(First Normal Form)
  + Each table cell (column + row) should contain a single value Each row (record) should be unique
+ 2NF(Second Normal Form) 
  + There are no duplicate row vlues because of multi-column keys (composite keys)
+ 3NF(third Normal Form)
  + All column values in a row are dependent on only the primary key
+ BCNF(3.5 NF) (Boyce-Codd Normal From) 
  + There must be no conflicting unique identification criteria(i.e. column value combination) -> Avoid multiple entities in one title
+ 4NF(Forth Normal Form) 
  + All combinations of all non-key) cell values should be possible
+ 5NF(Fifth Normal Form)
  + There are no clashing column values because of implicit column dependencies 



### Normalization For Humans 

Simple Rule:

Avoid mixing data centities in the same table, avoid ultiple values in a single table cell but also try to avoid splitting basic data across dozens of tables!



'''
select u.first_name, a.street, a.house_number, c.name from users as u
inner join addresses a on u.address_id = a.id inner join cities c on a.city_id = c.id;

'''

## Left join

'''
select u.first_name, a.street, a.house_number from addresses as a left join users as u on u.address_id = a.id ;


select u.first_name, a.street, a.house_number, c.name from addresses as a left join users as u on u.address_id = a.id left join cities c on a.city_id = c.id;


'''

## Right Join



## cross join


## Union


## Foreign Keys


'''
create table users (
    address_id int references addresses (id) on delete cascade
	)
'''


On delete & updatee

+ RESTRICT
  + Prevent the intended action(e.g. deleting a related row)
+ ON ACTION(default)
  + Prevent the intended action(e.g. deleting a related row) Check can be deferred, e.g. as part of a transaction
+ CASCADE
  + Perform the same action(e.g. deleting a related row) on the row with the foreign key
+ SET NULL
  + Set the foreign key value to NULL, if the related row was deleted
+ SET DEFAULT
  + Set the foreign key value to DEFAULT value if the related row was deleted


