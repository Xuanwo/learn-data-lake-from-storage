# How Data Lake Stores Table Metadata?

Welcome to the first chapter of Learn Data Lake From Storage! In this chapter, we will explore how data lakes store metadata. Please make sure you have finished the setup work for data lakes you want to explore.

## Apache Hive

> Visit [Apache Hive](lakes/apache-hive) to get it setup.

Let's go and create a table:

```sql
CREATE EXTERNAL TABLE example_table (
  id INT,
  name STRING
)
STORED AS PARQUET;
```

We will see:

```shell
:) sudo tree /var/lib/docker/volumes/apache-hive_warehouse/_data
/var/lib/docker/volumes/apache-hive_warehouse/_data
└── example_table

2 directories, 0 files
```

An empty directory is created for the table. No extra metadata file is created. So we can know all the table related metadata are stored in the hive metastore.

## Apache Iceberg

> Visit [Apache Iceberg](lakes/apache-iceberg) to get it setup.

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
