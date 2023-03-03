    show databases;
    create database hive_db;
    use hive_db;


```sql
create table department_data (
    dept_id int,
    dept_name string,
    manager_id int,
    salary int
)
row format delimited
fields terminated by ',';
```
    
describe department_data;

describe formatted department_data;

## For data load from local
    load data local inpath "file:////home/bigdata/git/Hive/depart_data.csv" into table department_data; 


## Display column name
```
set hive.cli.print.header = true;
```
> To drop the internal table 

    drop table department_data;

```sql
create table department_data (
    dept_id int,
    dept_name string,
    manager_id int,
    salary int
)
row format delimited
fields terminated by ',';
```

## Load data from hdfs location
> While loading from hdfs path no need to mention file keyword.

    load data inpath '/tmp/depart_data.csv' into table department_data;


## Command to create external tables

    create external table department_date_external
        (
        dept_id int,
        dept_name string,
        manager_id int,
        salary int
        )
        row format delimited
        fields terminated by ','
        location '/input_data/';
    
 ## work with Array data types


```sql
create table employee (
    emp_id int,
    name string,
    skills array<string>
)
row format delimited 
fields terminated by ','
collection items terminated by ':';
```
                                                                                               
```linux
load data local inpath 'file:///home/bigdata/git/Hive/array_data.csv' into table employee;
```

# Get element by index in hive array data type
```sql
select emp_id, name, skills[0] as prime_skill from employee;
```

```sql
select 
emp_id, 
name, 
size(skills) as size_of_each_array,
array_contains(skills,"HADOOP") as knows_hadoop,
sort_array(skills) as sorted_array
from employee;
```

### Table for map data example

```sql
create table employee_map_data
(
    id int,
    name string,
    details map<string,string>
)
row format delimited
fields terminated by ','
collection items terminated by '|'
map keys terminated by ':';
```
<pre>
101     Amit    {"age":"21","gender":"M"}
102     Sumit   {"age":"24","gender":"M"}
103     Mansi   {"age":"23","gender":"F"}
</pre>
> Here in the above query, we are creating map using two string data type because

* In the table we have multiple key value pair, so we have to upload uniform datatype(string) for the table.
* If incase we had only one pair of key value pair data, we can declare the same datatype.
> load the data from local to hive table

    load data local inpath 'file:///home/bigdata/git/Hive/map_data.csv' into table employee_map_data;

```sql
select id,
name,
details["gender"] as employee_gender
from employee_map_data;
```
<pre>
id      name    employee_gender
101     Amit    M
102     Sumit   M
103     Mansi   F
</pre>
 

### Map functions
```sql
select id,
name,
details,
size(details) as size_of_each_map,
map_keys(details) as distinct_map_keys,
map_values(details) as distinct_map_values
from employee_map_data;
```
<pre>
id      name    details size_of_each_map        distinct_map_keys       distinct_map_values
101     Amit    {"age":"21","gender":"M"}       2       ["age","gender"]        ["21","M"]
102     Sumit   {"age":"24","gender":"M"}       2       ["age","gender"]        ["24","M"]
103     Mansi   {"age":"23","gender":"F"}       2       ["age","gender"]        ["23","F"]
</pre>



    
  ### Dataset for assignment
  https://www.kaggle.com/datasets/imdevskp/corona-virus-report
