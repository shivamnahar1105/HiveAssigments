# Hive Class Assigment - 1

### Load data into hdfs 

```
hadoop fs -put sales_order_data.csv /tmp
```

### Create a internal hive table "sales_order_csv" which will store csv data sales_order_csv .. make sure to skip header row while creating table

```
create table sales_order_csv
(
ORDERNUMBER int,
QUANTITYORDERED int,
PRICEEACH FLOAT,
ORDERLINENUMBER int,
SALES int,
STATUS string,
QTR_ID int,
MONTH_ID int,
YEAR_ID int,
PRODUCTLINE string,
MSRP int,
PRODUCTCODE string,
PHONE int,
CITY int,
STATE string,
POSTALCODE int,
COUNTRY string,
TERRITORY string,
CONTACTLASTNAME string,
CONTACTFIRSTNAME string,
DEALSIZE string 
)
row format delimited
fields terminated by ','
tblproperties("skip.header.line.count"="1");
```
     
 
##### Load data from hdfs path into "sales_order_csv" 
     
```
load data inpath '/tmp/sales_order_data.csv' into table sales_order_csv;
```
<hr>  

#### Create an internal hive table which will store data in ORC format "sales_order_data_orc"

```
create table sales_order_data_orc 
( 
ORDERNUMBER int, QUANTITYORDERED int, 
PRICEEACH float, ORDERLINENUMBER int, 
SALES float, STATUS string, 
QTR_ID int, MONTH_ID int, 
YEAR_ID int, PRODUCTLINE string, 
MSRP int, PRODUCTCODE string, 
PHONE string, CITY string, 
STATE string, POSTALCODE string, 
COUNTRY string, TERRITORY string, 
CONTACTLASTNAME string, CONTACTFIRSTNAME string, 
DEALSIZE string 
) 
stored as orc;
```
#####  Load data from "sales_order_csv" into "sales_order_data_orc"

```
from sales_order_csv insert overwrite table sales_order_data_orc select *;
```

#### Perform below menioned queries on "sales_order_data_orc" table :

##### a. Calculate total sales per year

```
select year_id, sum(sales) as total_sales from sales_order_data_orc group by year_id;
```
##### b. Find a product for which maximum orders were placed

```
select PRODUCTCODE, sum(QUANTITYORDERED) as total_order from sales_order_data_orc GROUP BY PRODUCTCODE ORDER BY total_order DESC LIMIT 1;
```
##### c. Calculate the total sales for each quarter


     
