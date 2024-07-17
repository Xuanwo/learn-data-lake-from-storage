# Apache Iceberg

The open table format for analytic datasets.

Iceberg is a high-performance format for huge analytic tables. Iceberg brings the reliability and simplicity of SQL tables to big data, while making it possible for engines like Spark, Trino, Flink, Presto, Hive and Impala to safely work with the same tables, at the same time.

## Start Service

```shell
docker-compose up
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

At current time, the data path is empty:

```shell
:) s3cmd --access_key=admin --secret_key=password --host=127.0.0.1:9000 --host-bucket="127.0.0.1:9000/%(bucket)" --no-ssl ls -rl s3://warehouse


```

## Create Table

Let's go and create a table:

```sql
CREATE TABLE demo.nyc.example_table (
  id BIGINT,
  name STRING
);
```

A new metadata json file has been created under the `warehouse` bucket:

```shell
:) s3cmd --access_key=admin --secret_key=password --host=127.0.0.1:9000 --host-bucket="127.0.0.1:9000/%(bucket)" --no-ssl ls -rl s3://warehouse
2024-07-17 08:33         1207  876b1aa0d39759f1b6391319d412cabd     STANDARD     s3://warehouse/nyc/example_table/metadata/00000-37121756-df65-47d6-93bb-b6eb53e33eea.metadata.json
```

Let's get its content:

```shell
:) s3cmd --access_key=admin --secret_key=password --host=127.0.0.1:9000 --host-bucket="127.0.0.1:9000/%(bucket)" --no-ssl get s3://warehouse/nyc/example_table/metadata/00000-37121756-df65-47d6-93bb-b6eb53e33eea.metadata.json

:) cat 00000-37121756-df65-47d6-93bb-b6eb53e33eea.metadata.json

{
  "format-version": 1,
  "table-uuid": "996d3848-eebc-45eb-812b-8622f5106d9f",
  "location": "s3://warehouse/nyc/example_table",
  "last-updated-ms": 1721205204577,
  "last-column-id": 2,
  "schema": {
    "type": "struct",
    "schema-id": 0,
    "fields": [
      {
        "id": 1,
        "name": "id",
        "required": false,
        "type": "long"
      },
      {
        "id": 2,
        "name": "name",
        "required": false,
        "type": "string"
      }
    ]
  },
  "current-schema-id": 0,
  "schemas": [
    {
      "type": "struct",
      "schema-id": 0,
      "fields": [
        {
          "id": 1,
          "name": "id",
          "required": false,
          "type": "long"
        },
        {
          "id": 2,
          "name": "name",
          "required": false,
          "type": "string"
        }
      ]
    }
  ],
  "partition-spec": [],
  "default-spec-id": 0,
  "partition-specs": [
    {
      "spec-id": 0,
      "fields": []
    }
  ],
  "last-partition-id": 999,
  "default-sort-order-id": 0,
  "sort-orders": [
    {
      "order-id": 0,
      "fields": []
    }
  ],
  "properties": {
    "owner": "root"
  },
  "current-snapshot-id": -1,
  "refs": {},
  "snapshots": [],
  "statistics": [],
  "snapshot-log": [],
  "metadata-log": []
}
```

In this file, we have the table schema, location, and some other metadata. Although it's still possible for the catalog to cache or store this metadata, the metadata is stored in the storage layer.

## Insert Data

Let's insert some data:

```sql
INSERT INTO demo.nyc.example_table VALUES (1, "a");
```

Let's see how storage changed:

```shell
:) s3cmd --access_key=admin --secret_key=password --host=127.0.0.1:9000 --host-bucket="127.0.0.1:9000/%(bucket)" --no-ssl ls -r s3://warehouse
2024-07-17 08:37          643  s3://warehouse/nyc/example_table/data/00000-0-d58f591f-ad55-4273-92cc-ab822b979101-00001.parquet
2024-07-17 08:33         1207  s3://warehouse/nyc/example_table/metadata/00000-37121756-df65-47d6-93bb-b6eb53e33eea.metadata.json
2024-07-17 08:37         2239  s3://warehouse/nyc/example_table/metadata/00001-0516b8a5-a4d7-4d9e-ad21-7886bf787cda.metadata.json
2024-07-17 08:37         5773  s3://warehouse/nyc/example_table/metadata/3edad76b-0a9a-4a23-bc19-fe313581a137-m0.avro
2024-07-17 08:37         3755  s3://warehouse/nyc/example_table/metadata/snap-9006172241630323903-1-3edad76b-0a9a-4a23-bc19-fe313581a137.avro
```

Apart from existing `metadata/00000-37121756-df65-47d6-93bb-b6eb53e33eea.metadata.json`, we have the following new files created after an insert:

- `data/00000-0-d58f591f-ad55-4273-92cc-ab822b979101-00001.parquet`
- `metadata/00001-0516b8a5-a4d7-4d9e-ad21-7886bf787cda.metadata.json`
- `metadata/3edad76b-0a9a-4a23-bc19-fe313581a137-m0.avro`
- `metadata/snap-9006172241630323903-1-3edad76b-0a9a-4a23-bc19-fe313581a137.avro`

## TBD
