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
