# Problem 1
```
SELECT A.id,
       A.type,
       A.status,
       A.amount,
       (A.amount-B.avg_amt) AS difference
  FROM problem1.account A,
( SELECT type, AVG(amount) AS avg_amt
           FROM problem1.account
           where status = 'Closed'
          GROUP BY type
       ) B
 WHERE A.type = B.type
 AND A.status='Active'
 ;

```
# problem 2
```
CREATE external TABLE solution (
id INT,
fname STRING,
lname STRING,
address STRING,
city STRING,
state STRING,
zip STRING,
birthday STRING,
hireday STRING
)
STORED AS PARQUET
LOCATION '/user/training/problem2/data/employee/'
;

select * from problem2.solution;
```
# problem 3
```
create table solution (
id STRING,
fname STRING,
lname STRING,
hphone STRING
);

insert into table solution
select c.id as id,
c.fname as fname,
c.lname as lname,
translate(translate(translate(translate(c.hphone, '(', ''), ')', ''), ' ', ''), '-', '') as hphone
from customer c
where exists (
select a.custid from account a where a.amount > 0 and a.custid = c.id
);

```
# problem 4
```
```
# problem 5
```
INSERT OVERWRITE LOCAL DIRECTORY '/home/training/problem5/result'
ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
SELECT fname, lname, zip, city, state
  FROM problem5.customer
 WHERE city = 'Palo Alto'
   AND state = 'CA'
 UNION ALL
SELECT fname, lname, zip, city, state
  FROM problem5.employee
 WHERE city = 'Palo Alto'
   AND state = 'CA'

```
# problem 6
```
create table solution (
id INT,
fname STRING,
lname STRING,
address STRING,
city STRING,
state STRING,
zip STRING,
birthyear STRING
);

insert into table solution
select id, fname, lname, address, city, state, zip, substring(birthday, 7) as birthyear from employee;

select * from solution;
```

# problem 7
```
select concat(lname, ',', fname) as fullname from problem7.employee where city = 'Seattle'
order by fullname;
```
# problem 8
```
sqoop export --connect jdbc:mysql://localhost/problem8 --username cloudera --password cloudera --export-dir /user/training/problem8/data/customer --table solution --input-fields-terminated-by '\t'

```
# problem 9
```
CREATE TABLE solution
(
	  id STRING,
	  fname STRING,
	  lname STRING,
	  address STRING,
	  city STRING,
	  state STRING,
	  zip STRING,
	  birthday STRING
)

INSERT INTO TABLE solution
SELECT CONCAT('A', id),
	     fname,
	     lname,
	     address,
	     city,
	     state,
	     zip,
	     birthday
  FROM customer;

SELECT * FROM solution;
```
# problem 10
```
CREATE VIEW solution AS
SELECT A.id,
       A.fname,
       A.lname,
       A.city,
       A.state,
       B.charge,
       substr(B.tstamp,1,10) AS billdata
  FROM customer A, billing B
  where A.id = B.id;

select * from solution;	
```
