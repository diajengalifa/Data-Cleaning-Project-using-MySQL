# Data-Cleaning-Project-using-MySQL
# showing all databases
show databases;
# creating database for book store sold product
create database sales_data;
# choosing the book store database
use sales_data;

# creating table for January data
create table january_2019 
(
	order_id varchar(100),
    product varchar(100),
    quantity_ordered varchar(10),
    price_per_each decimal(5,2),
    order_date varchar(20),
	purchase_address varchar(100)
) engine = InnoDB;

# showing all tables
show tables;

# showing tables structure 
describe january_2019; 

#converting data types
ALTER table january_2019
	modify price_per_each varchar(50),
        modify quantity_ordered varchar(50);


#importing csv file
load data infile 'C:/ProgramData/MySQL/MySQL Server 8.0/Uploads/Sales_January_2019.csv' 
INTO TABLE january_2019 
FIELDS TERMINATED BY ',' 
ENCLOSED BY '"'
LINES TERMINATED BY '\n'
IGNORE 1 ROWS;

#table preview
select * from january_2019;

#splitting order_date to orderdate and ordertime (Individually)
select
SUBSTRING_INDEX(order_date,' ',1) AS orderdate,
SUBSTRING_INDEX(order_date,' ',-1) AS ordertime
FROM january_2019;

#Adding new columns to january_2019
alter table january_2019
add column orderdate varchar(10),
add column ordertime varchar(10);

select * from january_2019;

#Adding values to orderdate and ordertime columns
update january_2019
set orderdate = SUBSTRING_INDEX(order_date,' ',1);
update january_2019
set ordertime = SUBSTRING_INDEX(order_date,' ',-1);

#Changing the datatype of orderdate and ordertime columns

#splitting order_date to orderdate and ordertime (Individually)
select
SUBSTRING_INDEX(order_date,' ',1) AS orderdate,
SUBSTRING_INDEX(order_date,' ',-1) AS ordertime
FROM january_2019;

#Adding new columns to january_2019
alter table january_2019
add column orderdate varchar(10),
add column ordertime varchar(10);

select * from january_2019;

#Adding values to orderdate and ordertime columns
update january_2019
set orderdate = SUBSTRING_INDEX(order_date,' ',1);
update january_2019
set ordertime = SUBSTRING_INDEX(order_date,' ',-1);

#Changing datatypes of orderdate and ordertime
update january_2019
set orderdate = str_to_date(orderdate,'%m/%d/%y');

select * from january_2019 where orderdate = 'order';

Delete from january_2019 where orderdate = 'order';

#Changing datatypes of orderdate and ordertime
update january_2019
set orderdate = str_to_date(orderdate,'%m/%d/%y');
update january_2019
set ordertime = str_to_date(ordertime, '%H:%i');

select orderdate, ordertime from january_2019;

#Adding 'jan' value in front of order_id number

#Create new column for 'jan' values and add the values

alter table january_2019
		add column jan varchar (3);
update january_2019
set jan = 'jan';

#Adding jan values to order_id
update january_2019
set order_id = CONCAT(jan,'',order_id);

select order_id from january_2019;

CONVERTING DATA TYPE 
#converting data type
alter table january_2019
	modify quantity_ordered int;

#checking the data from specified rows 
select * from january_2019 LIMIT 664,1;

#checking the data that has the same error
select * from janaury_2019 where order_id = 'jan';

#deleting empty data
selete from january_2019 where order_id = 'jan';

#converting price_per_each data type
alter table january_2019 
	modify price_per_each real(6,2),
	modify orderdate date,
	modify odrdertime time;
desc january_2019;



#checking null values
select * from january_2019 where purchase_address is null or orderdate is null or ordertime is null;

#check data duplication
with check_duplicate as (
select *,
	row_number() over(partition by order_id, product, quantity_ordered, price_per_each, 
					  purchase_address, orderdate, ordertime) rownumb
from january_2019
order by order_id)
select *
from check_duplicate
where rownumb > 1;

#deleting data duplication
create table duplicate_data (select *,
	row_number() over(partition by order_id, product, quantity_ordered, price_per_each, 
					  purchase_address, orderdate, ordertime) rownumb
from january_2019
order by order_id);

 select * from duplicate_data
	where rownumb > 1;
 
delete from duplicate_data
	where rownumb > 1;

drop table january_2019;

alter table duplicate_data
		rename to january_2019;

select * from january_2019;



#splitting purchase address
select purchase_address from january_2019;

alter table january_2019
	add column street varchar(50),
    add column city varchar(30),
    add column state varchar(5),
    add column postal_code varchar(10);

select
SUBSTRING_INDEX(purchase_address,',',1) AS street1,
SUBSTRING_INDEX(SUBSTRING_INDEX(purchase_address,',',2),' ',-1) AS city1,
SUBSTRING_INDEX(SUBSTRING_INDEX(purchase_address,',',-1),' ',2) AS state1,
SUBSTRING_INDEX(purchase_address,' ',-1) AS postal_code1
FROM january_2019;

#Adding values to orderdate and ordertime columns
update january_2019
set street = SUBSTRING_INDEX(purchase_address,',',1);
update january_2019
set city = SUBSTRING_INDEX(SUBSTRING_INDEX(purchase_address,',',2),' ',-1);
update january_2019
set state = SUBSTRING_INDEX(SUBSTRING_INDEX(purchase_address,',',-1),' ',2);
update january_2019
set postal_code = SUBSTRING_INDEX(purchase_address,' ',-1);

select * from january_2019;


#creating categories for existed products 
select distinct product from january_2019;

alter table january_2019
	add column prod_category varchar(100);
    
update january_2019
set prod_category = case
	when product like '%Phone' then "cell phone"
    when product like '%Cable' then "cable"
    when product like '%Headphones' then "headphone"
    when product like '%Monitor' then "computer"
    when product like '%Batteries%' then "battery"
    when product like '%Laptop' then "laptop"
    when product like '%TV' then "TV"
    else "washing machine"
end;

select product, prod_category from january_2019;


#arranging column order 

select * from january_2019;

desc january_2019;

alter table january_2019
    modify prod_category varchar(30) after order_id,
    modify orderdate date after order_date,
    modify ordertime time after orderdate;
    
