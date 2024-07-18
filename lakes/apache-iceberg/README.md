# Apache Iceberg

The open table format for analytic datasets.

Iceberg is a high-performance format for huge analytic tables. Iceberg brings the reliability and simplicity of SQL tables to big data, while making it possible for engines like Spark, Trino, Flink, Presto, Hive and Impala to safely work with the same tables, at the same time.

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

Install `s3cmd` to inspect s3 object storage:

```shell
pip install s3cmd
```

Install avro-tools to inspect avro files:

```shell
curl https://downloads.apache.org/avro/avro-1.11.3/java/avro-tools-1.11.3.jar -O ./tools/avro-tools-1.11.3.jar
```

## Start Service

```shell
docker compose up
```

iceberg with spark as engine is ready after seeing logs like the following:

```shell
spark-iceberg  | [W 08:26:18.662 NotebookApp] WARNING: The notebook server is listening on all IP addresses and not using authentication. This is highly insecure and not recommended.
spark-iceberg  | [I 08:26:18.666 NotebookApp] Serving notebooks from local directory: /home/iceberg/notebooks
spark-iceberg  | [I 08:26:18.666 NotebookApp] Jupyter Notebook 6.5.4 is running at:
spark-iceberg  | [I 08:26:18.666 NotebookApp] http://529944937e93:8888/
spark-iceberg  | [I 08:26:18.666 NotebookApp] Use Control-C to stop this server and shut down all kernels (twice to skip confirmation).
```

This service also started a minio service with `admin:password` as access key and secret key. We can visit it via `http://127.0.0.1:9000`.

In our following examples, we will use `s3cmd` to interact with minio.

```shell
:) s3cmd --access_key=admin --secret_key=password --host=127.0.0.1:9000 --host-bucket="127.0.0.1:9000/%(bucket)" --no-ssl ls -rl s3://warehouse
```

## Connect to Iceberg via Spark

```shell
docker exec -it spark-iceberg spark-sql
```

We will get into the spark sql shell:

```shell
24/07/17 08:29:56 WARN ObjectStore: Version information not found in metastore. hive.metastore.schema.verification is not enabled so recording the schema version 2.3.0
24/07/17 08:29:56 WARN ObjectStore: setMetaStoreSchemaVersion called but recording version is disabled: version = 2.3.0, comment = Set by MetaStore UNKNOWN@172.19.0.5
24/07/17 08:29:56 WARN ObjectStore: Failed to get database default, returning NoSuchObjectException
Spark master: local[*], Application Id: local-1721204992221
spark-sql >
```

At current time, the data path is empty.

## Tips

### Inspect files from minio

List all files

```shell
s3cmd --access_key=admin --secret_key=password --host=127.0.0.1:9000 --host-bucket="127.0.0.1:9000/%(bucket)" --no-ssl ls -rl s3://warehouse
```

Download file

```shell
s3cmd --access_key=admin --secret_key=password --host=127.0.0.1:9000 --host-bucket="127.0.0.1:9000/%(bucket)" --no-ssl get s3://warehouse/nyc/example_table/metadata/00000-37121756-df65-47d6-93bb-b6eb53e33eea.metadata.json
```

### Inspect avro files

```shell
java -jar ./tools/avro-tools-1.11.3.jar tojson ./snap-7304560488408846027-1-631b4d7b-5501-4cb2-b205-7b8117a0fe7b.avro
```

Add `--pretty` to make it easier to read.

## Questions

1. [How Apache Iceberg Stores Table Metadata?](../../01-how-data-lake-stores-table-metadata.md#apache-iceberg)
2. [How Apache Iceberg Handles One Line Insert?](../../02-how-data-lake-handles-one-line-insert.md#apache-iceberg)