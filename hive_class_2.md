```sql
create table sales_data_v2 (
    p_type string,
    total_sales int
)
row format delimited
fields terminated by ',';
```
<pre>
hive> select * from sales_data_v2;
OK
clothes 1000
toys    2000
clocks  3000
</pre>

```
load data local inpath load data local inpath "file:///home/bigdata/git/Hive/sales_data_raw.csv" into table sales_data_v2;
```
# Command to create a new table from different table
```sql
create table sales_data_v2_bkup as select * from sales_data_v2;
```

## create table as CSV SerDe

```sql
create table csv_table (
    name string, location string)
    row format serde 'org.apache.hadoop.hive.serde2.OpenCSVSerde'
    with serdeproperties(
    "seperatorChar" = ",",
    "quoteChar" = "\"",
    "escapeChar" = "\\")
    stored as textfile
    tblproperties ("skip.header.line.count" = "1");
```
    
### load data from local
```
load data local inpath "file:///home/bigdata/git/Hive/csv_file.csv" into table csv_table;
```

### download hive catalog jar file , if serde libraries are not imported

https://repo1.maven.org/maven2/org/apache/hive/hcatalog/hive-hcatalog-core/0.14.0/

### run add jar command on hive shell
add jar file:///config/workspace/hive-hcatalog-core-0.14.0.jar;

## create json table

```sql
create table json_table (
    name string,
    id int,
    skills array<string>
)
row format serde 'org.apache.hive.hcatalog.data.JsonSerDe'
stored as textfile;
```
    
# load data into json

load data local inpath "file:///home/bigdata/git/Hive/json_file.json" into table json_table;

<pre>
Time taken: 0.384 seconds
hive> select * from json_table;
OK
Amit    1       ["Hadoop","Python"]
sumit   2       ["Hadoop","Hive"]
Shashank        3       ["Airflow","Python"]
Time taken: 0.232 seconds, Fetched: 3 row(s)
hive> select skills[1] from json_table;
OK
Python
Hive
Python
Time taken: 0.674 seconds, Fetched: 3 row(s)
</pre>

Table: json_table

|name|id|skills|
| - | - | - |
|Amit|1|["Hadoop","Python"]|
|sumit|2|["Hadoop","Hive"]|
|Shashank|3|["Airflow","Python"]|
    
## create a table which will store data in parquet

```sql
create table sales_data_pq_final
(
    product_type string,
    total_sales int
)
stored as parquet;
```
    
## load data in parquet file
from sales_data_v2 insert overwrite table sales_data_pq_final select *;

<pre>
hive> select * from sales_data_v2;
OK
clothes 1000
toys    2000
clocks  3000
Time taken: 0.212 seconds, Fetched: 3 row(s)
hive> from sales_data_v2 insert overwrite table sales_data_pq_final select *;
Query ID = bigdata_20230303191154_72ee75b9-244c-40fb-930b-74cba5e050a7
Total jobs = 3
Launching Job 1 out of 3
Number of reduce tasks determined at compile time: 1
In order to change the average load for a reducer (in bytes):
  set hive.exec.reducers.bytes.per.reducer=<number>
In order to limit the maximum number of reducers:
  set hive.exec.reducers.max=<number>
In order to set a constant number of reducers:
  set mapreduce.job.reduces=<number>
Starting Job = job_1677843846753_0002, Tracking URL = http://shiva-life:8088/proxy/application_1677843846753_0002/
Kill Command = /usr/local/hadoop/bin/mapred job  -kill job_1677843846753_0002
Hadoop job information for Stage-1: number of mappers: 1; number of reducers: 1
2023-03-03 19:12:04,508 Stage-1 map = 0%,  reduce = 0%
2023-03-03 19:12:10,752 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 3.4 sec
2023-03-03 19:12:16,996 Stage-1 map = 100%,  reduce = 100%, Cumulative CPU 6.23 sec
MapReduce Total cumulative CPU time: 6 seconds 230 msec
Ended Job = job_1677843846753_0002
Stage-4 is selected by condition resolver.
Stage-3 is filtered out by condition resolver.
Stage-5 is filtered out by condition resolver.
Moving data to directory hdfs://localhost:9000/user/hive/warehouse/hive_db.db/sales_data_pq_final/.hive-staging_hive_2023-03-03_19-11-54_581_4215453869683541437-1/-ext-10000
Loading data to table hive_db.sales_data_pq_final
MapReduce Jobs Launched: 
Stage-Stage-1: Map: 1  Reduce: 1   Cumulative CPU: 6.23 sec   HDFS Read: 15048 HDFS Write: 767 SUCCESS
Total MapReduce CPU Time Spent: 6 seconds 230 msec
OK
Time taken: 25.009 seconds
hive> select * from sales_data_pq_final;
OK
clothes 1000
toys    2000
clocks  3000
Time taken: 0.188 seconds, Fetched: 3 row(s)
</pre>