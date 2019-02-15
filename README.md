# RollerCoaster-Data-Analysis
The objective of the project is to analyze the RollerCoaster Dataset and solve various KPIs using HIVE.

### Table of contents:
- [Data Format](#data-format-all-the-fields-are-comma-delimited)
- [Prerequisites](#prerequisites)
- [Getting Started](#getting-started)
- KPIs
  - [KPI 1](#kpi-1)
  - [KPI 2](#kpi-2)
  - [KPI 3](#kpi-3)
  - [KPI 4](#kpi-4)
  - [KPI 5](#kpi-5)
  - [KPI 6](#kpi-6)
  - [KPI 7](#kpi-7)
  - [KPI 8](#kpi-8)
  - [KPI 9](#kpi-9)
  - [KPI 10](#kpi-10)
  - [KPI 11](#kpi-11)
  - [KPI 12](#kpi-12)
  - [KPI 13](#kpi-13)
  - [KPI 14](#kpi-14)
  - [KPI 15](#kpi-15)
  
### Data Format: All the fields are comma delimited
- park_id int,
- theme string,
- rollercoaster_type string,
- custom_design int,
- excitement double,
- excitement_rating string,
- intensity double,
- intensity_rating string,
- nausea double,
- nausea_rating string,
- max_speed double,
- avg_speed double,
- ride_time int,
- ride_length int,
- max_pos_gs double,
- max_neg_gs double,
- max_lateral_gs double,
- total_air_time double,
- drops int,
- highest_drop_height int,
- inversions int



### Prerequisites 
- Use Ubuntu or any flavour of Linux with following installed
   - Hadoop 2.x or above
   - JDK
   
   
### Getting Started:    
- Lets install Hive before starting with KPIs , you can get setup from here [Hive setup](http://archive.cloudera.com/cdh5/cdh/5/hive-0.13.1-cdh5.3.2.tar.gz)
- Just untar the tar.gz to install Hive.</br>
`tar xzf hive-0.13.1-cdh5.3.2.tar.gz `
- Start hadoop services. </br>
   ` sbin/start-all.sh `
- Start Hive shell. </br>
   ` bin/hive `
- create a table 
```mysql
create table rollercoaster
(
park_id int,
theme string,
rollercoaster_type string,
custom_design int,
excitement double,
excitement_rating string,
intensity double,
intensity_rating string,
nausea double,
nausea_rating string,
max_speed double,
avg_speed double,
ride_time int,
ride_length int,
max_pos_gs double,
max_neg_gs double,
max_lateral_gs double,
total_air_time double,
drops int,
highest_drop_height int,
inversions int
) row format delimited fields terminated by ',';
```

- Load data into Hive table from our local file system
```mysql
Load Data Local Inpath '/home/suraj/Desktop/rollercoasters.csv' Overwrite Into table rollercoaster;
```


### KPI 1
 - Number of rollercoaster type based on excitement and nausea and also print theme name
 ```mysql
 select theme, excitement_rating, nausea_rating , count(rollercoaster_type) from rollercoaster group by 
 excitement_rating, nausea_rating,theme;
  ```
### KPI 2
- No. of rollercoaster where grouping based on excitement level and drop height </br>
**a)** where excitement level is highest(very high) and drop_height>50 </br>
**b)** where excitement level is high
and drop_height>50 and also print the park_id.

```mysql
a) select excitement_rating, highest_drop_height , count(rollercoaster_type) from rollercoaster 
   where excitement_rating = 'Very High' and 
   highest_drop_height > 50 group by excitement_rating, highest_drop_height;
```
```mysql
b) select park_id,excitement_rating,highest_drop_height , count(rollercoaster_type)
   from rollercoaster  group by park_id, excitement_rating, highest_drop_height
   having excitement_rating = 'High' and highest_drop_height > 50;
```

### KPI 3
**a)** Find out the name of rollercoaster_type, excitement_level intensity _level and nausea_level where total_air_time is max and </br>
**b)** Find out the total_air_time of that row whose excitement_level intensity _level and nausea_level is similar to row where   total_air_time is maximum.

```mysql
a) select distinct rollercoaster_type,excitement_rating,intensity_rating , nausea_rating
   from rollercoaster as r1 where r1.total_air_time in (select max(r2.total_air_time) from rollercoaster as r2);
```
```mysql
b) select r1.total_air_time from rollercoaster as r1  inner join 

  (select distinct excitement_rating,intensity_rating , nausea_rating from rollercoaster as rc where 
   rc.total_air_time in (select max(total_air_time) from rollercoaster)) as t 

   on t.excitement_rating = r1.excitement_rating 
   and t.intensity_rating = r1.intensity_rating and t.nausea_rating = r1.nausea_rating;
```
### KPI 4
 **a)** Find out the name of rollercoaster_type, excitement_level ,intensity _level and nausea_level  where avg_speed is max and </br>
 **b)** Compare the max_speed of that row whose excitement_level
   intensity _level and nausea_level is similar to row where avg_speed is maximum.
```mysql
a) select rollercoaster_type,excitement_rating,intensity_rating,
   nausea_rating from rollercoaster r1 where r1.avg_speed in
   (select max(r2.avg_speed) from rollercoaster r2);
```

```mysql
b) select r3.max_speed from rollercoaster as r3 inner join (select excitement_rating,intensity_rating,
   nausea_rating from rollercoaster r1 where r1.avg_speed 
   in(select max(r2.avg_speed) from rollercoaster r2) ) as t  
   on r3.intensity_rating = t.intensity_rating 
   and r3.excitement_rating = t.excitement_rating and r3.nausea_rating = t.nausea_rating; 
```

### KPI 5
 - Find out the parkid and rollercoaster type where no of drop is greater than 10 and have same
excitement _level.
```mysql
select x.park_id,x.rollercoaster_type from (select park_id,rollercoaster_type ,excitement_rating 
from rollercoaster where drops>10 group by park_id,rollercoaster_type ,excitement_rating) as x;
```

### KPI 6
- Group rollercoaster_type based on custom_design where excitement level is high.
```mysql
select  custom_design ,rollercoaster_type  from rollercoaster where 
excitement_rating =  'High' group by  custom_design,rollercoaster_type;
```

### KPI 7
- If ride_length is greater than 2000 and max_speed is greater than 50 so what is the level of
excitement and nausea.
```mysql
Select distinct excitement_rating,nausea_rating from rollercoaster where ride_length > 2000 and max_speed >50;
```
### KPI 8
- Park_name(theme) where atleast 2 rides excitement level is high.
```mysql
Select x.theme from (select  theme,count(excitement_rating) from rollercoaster where excitement_rating = 'High' 
group by theme having count(excitement_rating)>=2 ) as x;
```
### KPI 9
- In which roller coaster ride excitement level and avg_speed is highest.
```mysql
Select rollercoaster_type from rollercoaster  r1 where excitement_rating = 'Very High'
and r1.avg_speed in (select max(r2.avg_speed ) from rollercoaster as r2 );
```
### KPI 10
- Name of Rollercoaster where total_air_time is greater than 5 but still excitement_level is not
very high.
```mysql
Select rollercoaster_type from rollercoaster where total_air_time>5 and excitement_rating <> 'Very High';
```
### KPI 11
- If ride_length is greater than 3000 then find out avg_speed and excitement_level , group
excitement_level based on avg_speed >40.
```mysql
select excitement_rating,avg_speed from rollercoaster
where ride_length >3000  and avg_speed>40 group by excitement_rating,avg_speed;
```
### KPI 12
- When max_pos> 4 and max_neg is >1 then find out the name of rollercoaster where
intensity_level is greater than excitement_level.
```mysql
Select rollercoaster_type from rollercoaster where max_pos_gs>4 and  max_neg_gs>1 and intensity > excitement;
```
### KPI 13
- When max_pos>= 4 and max_neg is >=1 count the no of rollercoaster grouping based on </br>
**a)** Intensity_level greater than equal or less than excitement_level and </br>
**b)** Find out the same when max_pos>= 4 and max_neg is >=1 condition is not true.
```mysql
a) Select count(distinct(rollercoaster_type)) ,intensity_rating from rollercoaster 
where max_pos_gs>=4 and max_neg_gs>=1 and intensity > excitement group by intensity_rating; 
```
```mysql
b) Select count(distinct(rollercoaster_type)) ,intensity_rating from rollercoaster
   where max_pos_gs<4 and max_neg_gs<1 and intensity > excitement group by intensity_rating;
```
### KPI 14
- When nausea_level is low that what is the value of excitement_level.
```mysql
select distinct excitement_rating  from rollercoaster where  nausea_rating = 'Low';
```

### KPI 15
- Group rollercoaster_type based on custom_design where intensity level is very high and
ride_length is greater than 2000.
```mysql
Select custom_design, rollercoaster_type from rollercoaster where intensity_rating= 'Very High' 
and  ride_length>2000 group by custom_design , rollercoaster_type ;
```




