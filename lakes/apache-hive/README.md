# Apache Hive

The Apache Hive ™ is a distributed, fault-tolerant data warehouse system that enables analytics at a massive scale and facilitates reading, writing, and managing petabytes of data residing in distributed storage using SQL.

## Setup

Install `parquet_tools` to inspect parquet files:

```shell
python -m -venv venv
source ./venv/bin/activate
pip install parquet_tools
```

## Start Service

```shell
docker-compose up
```

hive is ready after seeing log like the following:

```shell
hiveserver2  | 2024-07-17 08:15:39: Starting HiveServer2
hiveserver2  | SLF4J: Class path contains multiple SLF4J bindings.
hiveserver2  | SLF4J: Found binding in [jar:file:/opt/hive/lib/log4j-slf4j-impl-2.18.0.jar!/org/slf4j/impl/StaticLoggerBinder.class]
hiveserver2  | SLF4J: Found binding in [jar:file:/opt/tez/lib/slf4j-reload4j-1.7.36.jar!/org/slf4j/impl/StaticLoggerBinder.class]
hiveserver2  | SLF4J: Found binding in [jar:file:/opt/hadoop/share/hadoop/common/lib/slf4j-reload4j-1.7.36.jar!/org/slf4j/impl/StaticLoggerBinder.class]
hiveserver2  | SLF4J: See http://www.slf4j.org/codes.html#multiple_bindings for an explanation.
hiveserver2  | SLF4J: Actual binding is of type [org.apache.logging.slf4j.Log4jLoggerFactory]
hiveserver2  | Hive Session ID = 2438c433-e3f7-438c-ab9f-59e344431d47
hiveserver2  | Hive Session ID = 2714522d-5a6b-4c3f-8472-16b577c7d77a
```

The docker volume is also created and mount, we can use `docker volume inspect apache-hive_warehouse` to get where is the volume.

```shell
:) docker volume inspect apache-hive_warehouse
[
    {
        "CreatedAt": "2024-07-17T16:15:30+08:00",
        "Driver": "local",
        "Labels": {
            "com.docker.compose.project": "apache-hive",
            "com.docker.compose.version": "2.28.1",
            "com.docker.compose.volume": "warehouse"
        },
        "Mountpoint": "/var/lib/docker/volumes/apache-hive_warehouse/_data",
        "Name": "apache-hive_warehouse",
        "Options": null,
        "Scope": "local"
    }
]
```

## Connect to Hive

```shell
docker exec -it hiveserver2 beeline -u 'jdbc:hive2://hiveserver2:10000/'
```

We will get into the beeline shell:

```shell
Connecting to jdbc:hive2://hiveserver2:10000/
Connected to: Apache Hive (version 4.0.0)
Driver: Hive JDBC (version 4.0.0)
Transaction isolation: TRANSACTION_REPEATABLE_READ
Beeline version 4.0.0 by Apache Hive
0: jdbc:hive2://hiveserver2:10000/>
```

At current time, the data path is empty:

```shell
:) sudo tree /var/lib/docker/volumes/apache-hive_warehouse/_data
[sudo] password for xuanwo:
/var/lib/docker/volumes/apache-hive_warehouse/_data

0 directories, 0 files
```

## Tips

### Inspect files from docker volume

```shell
sudo cp /var/lib/docker/volumes/apache-hive_warehouse/_data .
```

## Questions

1. [How Apache Hive Stores Table Metadata?](../../01-how-data-lake-stores-table-metadata.md#apache-hive)
2. [How Apache Hive Handles One Line Insert?](../../02-how-data-lake-handles-one-line-insert.md#apache-hive)