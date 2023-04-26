## Scenario Based questions:
<hr>

#### 1. Will the reducer work or not if you use “Limit 1” in any HiveQL query?

Using "LIMIT 1" in a HiveQL query can affect the behavior of the reducer, depending on the specific query and the underlying data.

When a query contains a "LIMIT 1" clause, it instructs Hive to stop processing the data once it has found the first result that matches the query conditions. This means that Hive will not need to perform a full reduce step, since it only needs to return a single row of data. In this case, the reducer may not be used at all.

However, if the query contains an ORDER BY clause, or if it involves a join or other complex operations, Hive may still need to use the reducer to perform the necessary computations. In this case, the reducer will only process a single chunk of data (typically 128MB by default) at a time, but it will still be used.

So in summary, whether the reducer will be used or not when using "LIMIT 1" in a HiveQL query depends on the specific query and the underlying data. In some cases, the reducer may not be needed, but in other cases, it may still be used to perform the necessary computations.

#### 2. Suppose I have installed Apache Hive on top of my Hadoop cluster using default metastore configuration. Then, what will happen if we have multiple clients trying to access Hive at the same time? 

If you have installed Apache Hive on top of your Hadoop cluster using default metastore configuration, multiple clients trying to access Hive at the same time can potentially create resource contention issues and affect performance.

The default metastore configuration in Hive uses a local derby database, which is not designed for high concurrency or high availability scenarios. This means that multiple clients trying to access the same metastore database simultaneously can cause locks and other synchronization issues, leading to slow response times and potential data inconsistencies.

To mitigate these issues, you can consider using a shared metastore database such as MySQL or PostgreSQL, which are designed for higher concurrency and can provide better performance and scalability. Additionally, you can also consider using a load balancer to distribute the client requests across multiple Hive servers to handle the incoming requests more efficiently.

Another option is to use Hive Server 2, which provides a more scalable and concurrent architecture for handling multiple client requests. Hive Server 2 can support multiple clients accessing the same metastore database and can handle the incoming requests using a pool of worker threads, thereby improving the overall performance and concurrency of the system.

#### 3. Suppose, I create a table that contains details of all the transactions done by the customers: CREATE TABLE transaction_details (cust_id INT, amount FLOAT, month STRING, country STRING) ROW FORMAT DELIMITED FIELDS TERMINATED BY ‘,’ ; Now, after inserting 50,000 records in this table, I want to know the total revenue generated for each month. But, Hive is taking too much time in processing this query. How will you solve this problem and list the steps that I will be taking in order to do so?

To optimize the query and improve the processing time, you can consider implementing the following steps:

* Use partitioning: Partitioning the table on the "month" column can help to improve the performance of the query, as Hive can scan only the relevant partition(s) instead of scanning the entire table. You can create the partitioned table using the following command:

```
CREATE TABLE transaction_details_partitioned (cust_id INT, amount FLOAT, country STRING)
PARTITIONED BY (month STRING)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ‘,’ ;
```

You can then insert the data into the partitioned table using the following command:

```
INSERT INTO TABLE transaction_details_partitioned PARTITION (month)
SELECT cust_id, amount, country, month FROM transaction_details;
```

* Use bucketing: Bucketing the table on the "month" column can further improve the performance of the query by reducing the amount of data that needs to be scanned for each bucket. You can create the bucketed table using the following command:

```
CREATE TABLE transaction_details_bucketed (cust_id INT, amount FLOAT, country STRING)
CLUSTERED BY (month) INTO 12 BUCKETS
ROW FORMAT DELIMITED FIELDS TERMINATED BY ‘,’ ;
```

You can then insert the data into the bucketed table using the following command:

```
INSERT INTO TABLE transaction_details_bucketed SELECT cust_id, amount, country, month FROM transaction_details;
```

* Use the correct query: To get the total revenue generated for each month, you can use the following query:

```
SELECT month, SUM(amount) as total_revenue FROM transaction_details_partitioned
GROUP BY month;
```

* Tune the configuration: You can also tune the Hive configuration settings to improve the performance of the query. Some of the important settings to consider include:

-- MapReduce parameters such as the number of mappers and reducers

-- Memory allocation parameters such as the heap size and cache size

-- Compression settings such as block size and codec type

-- Tez parameters such as the number of vertices and parallelism

By implementing these steps, you should be able to improve the performance of the query and get the total revenue generated for each month more efficiently.

#### 4.How can you add a new partition for the month December in the above partitioned table?

To add a new partition for the month of December in the above partitioned table "transaction_details_partitioned", you can use the following command:

```
ALTER TABLE transaction_details_partitioned ADD PARTITION (month='December');
```

This command will add a new partition for the month of December in the "transaction_details_partitioned" table. Once the partition is added, you can insert the data for December into the new partition using the following command:

```
INSERT INTO TABLE transaction_details_partitioned PARTITION (month='December')
SELECT cust_id, amount, country FROM transaction_details WHERE month='December';
```

This command will insert the data for the month of December from the "transaction_details" table into the new partition of "transaction_details_partitioned" table.

Note that you should ensure that the partition column values in the insert statement match the partition column names and values specified in the partitioned table.

#### 5.I am inserting data into a table based on partitions dynamically. But, I received an error – FAILED ERROR IN SEMANTIC ANALYSIS: Dynamic partition strict mode requires at least one static partition column. How will you remove this error?

To remove this error, you can either disable the dynamic partition mode or add at least one static partition column to the table. Here are the two approaches:

* **Disable dynamic partition mode**: If you don't require dynamic partitioning for your use case, you can disable dynamic partition mode in Hive using the following command:

```
SET hive.exec.dynamic.partition.mode=nonstrict;
```

This command will disable dynamic partition mode, allowing you to insert data into the partitioned table without any static partition columns. Note that this approach may impact performance, as Hive will scan the entire dataset instead of using partition pruning.

* **Add a static partition column**: If you require dynamic partitioning for your use case, you can add at least one static partition column to the table to remove the error. A static partition column is a column that has a fixed set of values that are defined at the time of table creation.


#### 6. Suppose, I have a CSV file – ‘sample.csv’ present in ‘/temp’ directory with the following entries: id first_name last_name email gender ip_address How will you consume this CSV file into the Hive warehouse using built-in SerDe?

To consume a CSV file using the built-in SerDe in Hive, you can follow these steps:

* Create a Hive table with the desired schema. You can use the following command to create a table:

```
CREATE TABLE sample_table (
  id INT,
  first_name STRING,
  last_name STRING,
  email STRING,
  gender STRING,
  ip_address STRING
) 
ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.OpenCSVSerde'
WITH SERDEPROPERTIES (
  'separatorChar' = ',',
  'quoteChar' = '\"'
) 
STORED AS TEXTFILE;
```

This command creates a table "sample_table" with the same columns as in the CSV file. The ROW FORMAT SERDE clause specifies the SerDe to use for reading the data from the CSV file. Here, we are using the built-in OpenCSVSerde, which is capable of reading CSV files with a comma as the separator character and double quotes as the quote character. The SERDEPROPERTIES clause specifies the separator and quote characters.

* Load data into the table from the CSV file using the following command:

```
LOAD DATA INPATH '/temp/sample.csv' INTO TABLE sample_table;
```

This command loads the data from the CSV file "/temp/sample.csv" into the table "sample_table". The data is stored as text files in HDFS, which is the default storage format for Hive tables.

#### 7. Suppose, I have a lot of small CSV files present in the input directory in HDFS and I want to create a single Hive table corresponding to these files. The data in these files are in the format: {id, name, e-mail, country}. Now, as we know, Hadoop performance degrades when we use lots of small files. So, how will you solve this problem where we want to create a single Hive table for lots of small files without degrading the performance of the system?

To create a single Hive table for lots of small CSV files without degrading the performance of the system, you can use the following approach:

* Combine the small CSV files into larger files: You can use the Hadoop command hdfs dfs -getmerge to merge multiple small files into a larger file. For example, if you have multiple CSV files in the directory /input in HDFS, you can merge them into a single file using the following command:

```
hdfs dfs -getmerge /input merged_file.csv
```

This command will merge all the files in the /input directory into a single file named merged_file.csv.

* Load the merged file into a Hive table: Once you have merged the small CSV files into a larger file, you can load the merged file into a Hive table using the same approach as described in the previous answer. For example, you can use the following command to create a Hive table:

```
CREATE TABLE my_table (
  id INT,
  name STRING,
  email STRING,
  country STRING
) 
ROW FORMAT DELIMITED 
FIELDS TERMINATED BY ',' 
STORED AS TEXTFILE;
```

This command creates a table named my_table with the desired schema.

* Load data into the Hive table: After creating the table, you can load data from the merged file into the table using the following command:

```
LOAD DATA LOCAL INPATH '/path/to/merged_file.csv' INTO TABLE my_table;
```

This command loads the data from the merged file into the table my_table.

By combining the small CSV files into larger files, you can reduce the number of input files processed by Hadoop, which can improve the performance of the system. However, you should be careful not to create files that are too large, as this can also degrade performance. A good rule of thumb is to keep the file size between 128 MB and 1 GB.

#### 8. LOAD DATA LOCAL INPATH ‘Home/country/state/’ OVERWRITE INTO TABLE address; The following statement failed to execute. What can be the cause?

The LOAD DATA LOCAL INPATH statement you provided has an issue in the path specified. It is missing the root directory (/) at the beginning of the path. The correct statement should be:

```
LOAD DATA LOCAL INPATH '/Home/country/state/' OVERWRITE INTO TABLE address;
```

The missing root directory (/) at the beginning of the path could be the reason for the statement's failure to execute. When specifying the path in a Hive command, it is important to provide the complete path to the source data location, starting with the root directory (/).

Additionally, make sure that the path you provided is correct and that the data files are present in that location. If the path is incorrect or the files are not present, the command will fail.

#### 9. Is it possible to add 100 nodes when we already have 100 nodes in Hive? If yes, how?

Yes, it is possible to add 100 nodes to a Hive cluster that already has 100 nodes. To add more nodes, you can follow these general steps:

* Set up the new nodes: You will need to set up the new nodes as part of your Hadoop cluster. This involves installing the Hadoop software, configuring the nodes to work with your existing cluster, and ensuring that they can communicate with the other nodes in the cluster.

* Add the nodes to the cluster: Once the new nodes are set up, you can add them to the Hadoop cluster. This involves updating the Hadoop configuration files to include the new nodes and starting the Hadoop services on the new nodes.

* Rebalance the cluster: After adding the new nodes, you will need to rebalance the Hadoop cluster to distribute the data and workload evenly across all nodes. This can be done using the hdfs dfsadmin -report command to check the status of the cluster and the hdfs balancer command to initiate the rebalancing process.

* Verify the configuration: Finally, you should verify that the new nodes are properly configured and functioning as expected. You can check the cluster status using the Hadoop web interfaces or by running commands like hdfs dfs -ls or yarn node -list.

<hr>

## Hive Practical questions:

<hr>

#### 1.Hive Join operations

Create a  table named CUSTOMERS(ID | NAME | AGE | ADDRESS   | SALARY)
Create a Second  table ORDER(OID | DATE | CUSTOMER_ID | AMOUNT)

Now perform different joins operations on top of these tables
(Inner JOIN, LEFT OUTER JOIN ,RIGHT OUTER JOIN ,FULL OUTER JOIN)

<hr>

* Create table CUSTOMERS:

```
CREATE TABLE CUSTOMERS (
  ID INT,
  NAME STRING,
  AGE INT,
  ADDRESS STRING,
  SALARY INT
);
```

* Create table ORDERS:

```
CREATE TABLE ORDERS (
  OID INT,
  DATE STRING,
  CUSTOMER_ID INT,
  AMOUNT INT
);
```

> Inner Join: The inner join returns only the rows that have matching values in both tables.

```
SELECT *
FROM CUSTOMERS
JOIN ORDERS
ON CUSTOMERS.ID = ORDERS.CUSTOMER_ID;
```

> Left Outer Join: The left outer join returns all the rows from the left table (i.e., the table specified before the LEFT OUTER JOIN keyword) and the matching rows from the right table. If there is no match, the right table columns will contain null values.

```
SELECT *
FROM CUSTOMERS
LEFT OUTER JOIN ORDERS
ON CUSTOMERS.ID = ORDERS.CUSTOMER_ID;
```

> RIGHT OUTER JOIN: 

```
SELECT c.NAME, c.AGE, o.OID, o.DATE, o.AMOUNT
FROM CUSTOMERS c
RIGHT OUTER JOIN ORDERS o ON c.ID = o.CUSTOMER_ID;
```

This query will return all the records from the ORDERS table and the matching records from the CUSTOMERS table. If there are no matching records in the CUSTOMERS table, then NULL values will be returned for the columns of the CUSTOMERS table.


> FULL OUTER JOIN: 

```
SELECT c.NAME, c.AGE, o.OID, o.DATE, o.AMOUNT
FROM CUSTOMERS c
FULL OUTER JOIN ORDERS o ON c.ID = o.CUSTOMER_ID;
```

This query will return all the records from both the CUSTOMERS and ORDERS tables. If there are no matching records in either of the tables, then NULL values will be returned for the columns of the respective table.

### BUILD A DATA PIPELINE WITH HIVE

##### Create a hive table as per given schema in your dataset 


```
create table events (
id int,
sport string,
discipline string,
name string,
sex string,
venues string
)
row format serde 'org.apache.hadoop.hive.serde2.OpenCSVSerde'
with serdeproperties (
"separatorChar" = ",",
"quoteChar" = "\"",
"escapeChar" = "\\"
)
stored as textfile
tblproperties ("skip.header.line.count" = "1");
```

##### try to place a data into table location

```
hadoop fs -put events.csv /user/hive/warehouse/hive_db.db
```

Loading data from hdfs to tables events:

```
load data inpath '/user/hive/warehouse/hive_db.db/events.csv' into table events;
```

##### Perform a select operation . 

```
set hive.cli.print.header=True;
select * from events;
```

##### 

Fetch the result of the select operation in your local as a csv file . 

```
INSERT OVERWRITE LOCAL DIRECTORY '/Users/shivamnahar/Downloads' ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' SELECT * FROM events;
```
<img width="1133" alt="Screenshot 2023-04-26 at 8 59 34 AM" src="https://user-images.githubusercontent.com/118034802/234462947-f428ba17-69a3-48a4-8215-671b248a758e.png">



##### Perform group by operation . 

```
select count(discipline), sex from events group by sex;
```

##### Perform filter operation at least 5 kinds of filter examples . 

* where

```
SELECT * FROM events WHERE sport = 'aquatics';
```

* comparison 

```
SELECT * FROM events WHERE id = 519818 and name = 'Men''s 100m Breaststroke';
```

* Like 

```
SELECT * FROM events WHERE name LIKE  '%Men%';
```

* IN

```
SELECT * FROM events WHERE id IN  (701492, 305278, 729643, 567019);
```

* BETWEEN

```
SELECT * FROM events WHERE  ID  BETWEEN 708010 AND 567019;
```

##### alter table operation 

* drop table:

```
drop table events;
```

* order by operation:

```
SELECT * FROM events order by id;
```

* where clause operations you have to perform:

```
SELECT * FROM events WHERE sport = 'aquatics';
```

* sorting operation examples in hive:

```
SELECT * FROM events order by id desc;
```

* distinct operation you have to perform:

```
select distinct(sport) from events;
```

* like:

```
SELECT * FROM events WHERE name LIKE  '%Men%';
```

* Table view operation:

```
DESCRIBE events;
```







  












































