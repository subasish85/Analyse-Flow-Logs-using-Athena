#### Analyse-Flow-Logs-using-AWS-Athena #######
CREATE EXTERNAL TABLE IF NOT EXISTS default.vpc_flow_logs (
         version int,
         account string,
         interfaceid string,
         sourceaddress string,
         destinationaddress string,
         sourceport int,
         destinationport int,
         protocol int,
         numpackets int,
         numbytes bigint,
         starttime int,
         endtime int,
         action string,
         logstatus string 
) PARTITIONED BY (
        dt string
) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' LOCATION 's3://cfst-3029-{Your S3 URI}/us-east-1/{REMOVE THE DATE}' TBLPROPERTIES ("skip.header.line.count"="1");


##### IN ORDER TO READ THE DATA YOU HAVE TO PARTITION IT ###############

ALTER TABLE default.vpc_flow_logs 
ADD PARTITION (dt='2021-04-21') 
location 's3://cfst-{Your S3 URI}/us-east-1/2021/04/21/';

#### Now Start Analyzing the VPC Flow Logs Data in Athena #####
#### Open a new query window and paste in the below following Query it will filter by Reject with Protocol - TCP #####

SELECT day_of_week(from_iso8601_timestamp(dt)) AS
     day,
     dt,
     interfaceid,
     sourceaddress,
     destinationport,
     action,
     protocol
   FROM vpc_flow_logs
   WHERE action = 'REJECT' AND protocol = 6
   order by sourceaddress
   LIMIT 100;
