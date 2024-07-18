# Apache Paimon

A lake format that enables building a Realtime Lakehouse Architecture with Flink and Spark for both streaming and batch operations. Innovatively combines lake format and LSM structure, bringing realtime streaming updates into the lake architecture.

## Setup

Setup python venv

```shell
python -m -venv venv
source ./venv/bin/activate
```

Install `parquet_tools` to inspect parquet files:

```shell
pip install parquet_tools
```

Install avro-tools to inspect avro files:

```shell
curl https://downloads.apache.org/avro/avro-1.11.3/java/avro-tools-1.11.3.jar -O ./tools/avro-tools-1.11.3.jar
```

Install `paimon-flink` and `flink-shaded-hadoop-2-uber` jars:

```shell
curl https://repository.apache.org/content/groups/snapshots/org/apache/paimon/paimon-flink-1.17/0.9-SNAPSHOT/paimon-flink-1.17-0.9-20240715.002309-36.jar -O  ./lakes/apache-paimon/jars/paimon-flink-1.17-0.9-20240715.002309-36.jar

curl https://repo.maven.apache.org/maven2/org/apache/flink/flink-shaded-hadoop-2-uber/2.8.3-10.0/flink-shaded-hadoop-2-uber-2.8.3-10.0.jar -O ./lakes/apache-paimon/jars/flink-shaded-hadoop-2-uber-2.8.3-10.0.jar
```

## Start Service

```shell
docker compose up
```

paimon with flink as engine is ready after seeing logs like the following:

```shell
askmanager-1  | 2024-07-18 07:14:46,903 INFO  org.apache.flink.runtime.taskexecutor.DefaultJobLeaderService [] - Start job leader service.
taskmanager-1  | 2024-07-18 07:14:46,904 INFO  org.apache.flink.runtime.filecache.FileCache                 [] - User file cache uses directory /tmp/flink-dist-cache-fc74d147-8fe0-464d-84e7-9371f31056a6
taskmanager-1  | 2024-07-18 07:14:46,906 INFO  org.apache.flink.runtime.taskexecutor.TaskExecutor           [] - Connecting to ResourceManager akka.tcp://flink@jobmanager:6123/user/rpc/resourcemanager_*(00000000000000000000000000000000).
taskmanager-1  | 2024-07-18 07:14:47,002 INFO  org.apache.flink.runtime.taskexecutor.TaskExecutor           [] - Resolved ResourceManager address, beginning registration
jobmanager-1   | 2024-07-18 07:14:47,039 INFO  org.apache.flink.runtime.resourcemanager.StandaloneResourceManager [] - Registering TaskManager with ResourceID 172.20.0.4:34417-318d31 (akka.tcp://flink@172.20.0.4:34417/user/rpc/taskmanager_0) at ResourceManager
taskmanager-1  | 2024-07-18 07:14:47,046 INFO  org.apache.flink.runtime.taskexecutor.TaskExecutor           [] - Successful registration at resource manager akka.tcp://flink@jobmanager:6123/user/rpc/resourcemanager_* under registration id c1b9e510985b54e240acde372f3b97da.
```

## Connect to Paimon via Flink

```shell
docker compose run sql-client
```

We will get into the flink sql shell:

```shell
    ______ _ _       _       _____  ____  _         _____ _ _            _  BETA
   |  ____| (_)     | |     / ____|/ __ \| |       / ____| (_)          | |
   | |__  | |_ _ __ | | __ | (___ | |  | | |      | |    | |_  ___ _ __ | |_
   |  __| | | | '_ \| |/ /  \___ \| |  | | |      | |    | | |/ _ \ '_ \| __|
   | |    | | | | | |   <   ____) | |__| | |____  | |____| | |  __/ | | | |_
   |_|    |_|_|_| |_|_|\_\ |_____/ \___\_\______|  \_____|_|_|\___|_| |_|\__|

        Welcome! Enter 'HELP;' to list all available commands. 'QUIT;' to exit.

Command history file path: /opt/flink/.flink-sql-history

Flink SQL>
```

## Tips

### Inspect avro files

```shell
java -jar ./tools/avro-tools-1.11.3.jar tojson ./snap-7304560488408846027-1-631b4d7b-5501-4cb2-b205-7b8117a0fe7b.avro
```

Add `--pretty` to make it easier to read.

## Questions

1. [How Apache Paimon Stores Table Metadata?](../../01-how-data-lake-stores-table-metadata.md#apache-paimon)
2. [How Apache Paimon Handles One Line Insert?](../../02-how-data-lake-handles-one-line-insert.md#apache-paimon)