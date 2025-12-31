# seatunnel_demo

# Docker Compose

## network 
OOXXOOXX 自行取代成真正要進入的 network  
例：現在有一個運行的專案，也是用 docker compose 啟動，可以用這方式加入該專案network  
```
docker network ls

docker network inspect OOXXOOXX

dcoker inspect DOCKER_ID
```
讓 seatuneel container 認得運行的專案中的資料庫  

## jdbc
因為無法刪除，只好用新版取代舊版

```
- ./lib/mssql-jdbc-13.2.1.jre8.jar:/opt/seatunnel/lib/mssql-jdbc-9.2.1.jre8.jar
```

# config

## seatunnel.yaml
### checkpoint 
checkpoint_storage 要 stream 模式會用到，現在使用的是 job 不會有資料  

### basic-auth
basic-auth-name 和 basic-auth-password 可自行替換  

## hhazelcast.yaml
### rest-api
這會影響實際操作，必要時參考文件  
[rest-api-v1#overview](https://seatunnel.apache.org/docs/2.3.12/seatunnel-engine/rest-api-v1#overview)
v2 文件少一些內容，實際上是要看 v1 但執行時要參考 v2  

### join
這部份是因為 single noe，如果走 master、worker 自行調整  

## lo4j2.properties
主要調整
```
############################ log output to file    #############################
#rootLogger.appenderRef.file.ref = fileAppender
rootLogger.appenderRef.file.ref = routingAppender
############################ log output to file    #############################
```
這才會讓每個 job 獨立 log  
完整內容來自 docker container /opt/seatunnel/config/log4j2.propertie  
要看到原始只要把 docker-compose  
```
- ./config/log4j2.properties:/opt/seatunnel/config/log4j2.properties
```
註解掉就行  

# lib
## MSSQL
[官方下載](https://seatunnel.apache.org/docs/2.3.12/connector-v2/source/SqlServer)  
[JDBC 官方下載](https://mvnrepository.com/artifact/com.microsoft.sqlserver/mssql-jdbc)

# test MSSQL
建議採用這模式，因為 cli 方式在之後的操作是不方便的  
[test_mssql](./test_mssql.txt)  

```
curl -X POST http://localhost:5801/hazelcast/rest/maps/submit-job?jobName=SQLServer_to_Console \
-H "Content-Type: application/json" \
-d '
{
    "env": {
        "job.mode": "BATCH",
        "parallelism": 1
    },
    "source": [
        {
            "plugin_name": "Jdbc",
            "driver": "com.microsoft.sqlserver.jdbc.SQLServerDriver",
            "url": "jdbc:sqlserver://mssql:1433;databaseName=OOOOO;encrypt=false",
            "username": "sa",
            "password": "OOOOOOOOOOO",
            "query": "SELECT TOP 1 1 as XXXXXX"
        }
    ],
    "transform": [],
    "sink": [
        {
            "plugin_name": "Console"
        }
    ]
}
'
```
## jobName
可以自行替換  

## url
### mssql
記得 mssql 要取代成 真實操作的 DB ，在 docker-compose 裡面可以直接使用 名稱  

### databaseName
OOOOO 換成真實使用的  

### password
OOOOOOOOOOO 換成真實使用的  

### query
XXXXXX 換成真實使用的  

## 實際執行
到 webui 看執行結果  

或是 進到 docker container 內的 logs 找 ConsoleSinkWriter  

