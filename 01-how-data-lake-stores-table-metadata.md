# How Data Lake Stores Table Metadata?

Welcome to the first chapter of Learn Data Lake From Storage! In this chapter, we will explore how data lakes store metadata. Please make sure you have finished the setup work for data lakes you want to explore.

## Apache Hive

> Visit [Apache Hive](lakes/apache-hive/README.md) to get it setup.

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

> Visit [Apache Iceberg](lakes/apache-iceberg/README.md) to get it setup.

Let's go and create a table:

```sql
CREATE TABLE demo.nyc.example_table (
  id BIGINT,
  name STRING
);
```

A new metadata json file has been created under the `warehouse` bucket:

```shell
2024-07-17 13:14         1207  s3://warehouse/nyc/example_table/metadata/00000-c8902826-08a4-443f-a4ae-d6d62c153816.metadata.json
```

Let's get its content:

```shell
:) cat 00000-c8902826-08a4-443f-a4ae-d6d62c153816.metadata.json

{
  "format-version" : 1,
  "table-uuid" : "5500a886-c012-4d5a-b3de-78214eae9119",
  "location" : "s3://warehouse/nyc/example_table",
  "last-updated-ms" : 1721222050498,
  "last-column-id" : 2,
  "schema" : {
    "type" : "struct",
    "schema-id" : 0,
    "fields" : [ {
      "id" : 1,
      "name" : "id",
      "required" : false,
      "type" : "long"
    }, {
      "id" : 2,
      "name" : "name",
      "required" : false,
      "type" : "string"
    } ]
  },
  "current-schema-id" : 0,
  "schemas" : [ {
    "type" : "struct",
    "schema-id" : 0,
    "fields" : [ {
      "id" : 1,
      "name" : "id",
      "required" : false,
      "type" : "long"
    }, {
      "id" : 2,
      "name" : "name",
      "required" : false,
      "type" : "string"
    } ]
  } ],
  "partition-spec" : [ ],
  "default-spec-id" : 0,
  "partition-specs" : [ {
    "spec-id" : 0,
    "fields" : [ ]
  } ],
  "last-partition-id" : 999,
  "default-sort-order-id" : 0,
  "sort-orders" : [ {
    "order-id" : 0,
    "fields" : [ ]
  } ],
  "properties" : {
    "owner" : "root"
  },
  "current-snapshot-id" : -1,
  "refs" : { },
  "snapshots" : [ ],
  "statistics" : [ ],
  "snapshot-log" : [ ],
  "metadata-log" : [ ]
}
```

In this file, we have the table schema, location, and some other metadata. Although it's still possible for the catalog to cache or store this metadata, the metadata is stored in the storage layer.
