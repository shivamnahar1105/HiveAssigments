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
     
     ### Load data from hdfs path into "sales_order_csv" 
     
     ```
     load data inpath '/tmp/sales_order_data.csv' into table sales_order_csv;
     ```
     
     
