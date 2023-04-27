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

#### 4. Find out agent average rating.

```
Select agent_name, avg(avg_rating) as average_rating from agent_performance group by agent_name;
```

#### 5. Total working days for each agents 

```
select agent, count(distinct my_date) as total_working_days from agent_logging group by agent;
```

#### 6. Total query that each agent have taken 

```
Select agent_name, count(total_chats) as total_query from agent_performance group by agent_name;
```

#### 7. Total Feedback that each agent have received 

```
Select agent_name, count(total_feedback) as total_feedback from agent_performance group by agent_name;
```

#### 8. Agent name who have average rating between 3.5 to 4 

```
SELECT agent_name, average_rating
FROM (
  SELECT agent_name, AVG(avg_rating) AS average_rating
  FROM agent_performance
  GROUP BY agent_name
) t
WHERE average_rating BETWEEN 3.5 AND 4;
```


#### 9. Agent name who have rating less than 3.5

```
SELECT agent_name, average_rating
FROM (
  SELECT agent_name, AVG(avg_rating) AS average_rating
  FROM agent_performance
  GROUP BY agent_name
) t
WHERE average_rating < 3.5;
```

#### 10. Agent name who have rating more than 4.5 

```
SELECT agent_name, average_rating
FROM (
  SELECT agent_name, AVG(avg_rating) AS average_rating
  FROM agent_performance
  GROUP BY agent_name
) t
WHERE average_rating > 4.5;
```


#### 11. How many feedback agents have received more than 4.5 average

```
SELECT agent_name, count(total_feedback)
FROM agent_performance
GROUP BY agent_name
WHERE average_rating > 4.5;
```











