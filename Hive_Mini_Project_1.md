## Hive Mini Project-1

#### 1. Create a schema based on the given dataset

```
create table agent_performance
(
sl_no int,
my_date date,
agent_name string,
total_chats int,
avg_response_time TIMESTAMP,
avg_resolution_time TIMESTAMP,
avg_rating float,
total_feedback int
)
row format serde 'org.apache.hadoop.hive.serde2.OpenCSVSerde'
with serdeproperties (
"separatorChar" = ",",
"quoteChar" = "\"",
"escapeChar" = "\\",
"date.format"="MM/dd/yyyy"
)
stored as textfile
tblproperties ("skip.header.line.count" = "1");
```


```
create table agent_logging
(
sl_no int,
agent string,
my_date date,
login_time TIMESTAMP,
logout_time TIMESTAMP,
duration TIMESTAMP
)
row format serde 'org.apache.hadoop.hive.serde2.OpenCSVSerde'
with serdeproperties (
"separatorChar" = ",",
"quoteChar" = "\"",
"escapeChar" = "\\",
"date.format"="dd-MM-yy"
)
stored as textfile
tblproperties ("skip.header.line.count" = "1");
```

#### 2. Dump the data inside the hdfs in the given schema location.

```
load data local inpath 'file:///config/workspace/AgentLogingReport.csv' into table agent_logging;
```

```
load data local inpath 'file:///config/workspace/AgentPerformance.csv' into table agent_performance;
```

#### 3. List of all agents' names.

```
Select distinct(agent) from agent_logging;
```

```
Select distinct(agent_name) from agent_performance;
```
