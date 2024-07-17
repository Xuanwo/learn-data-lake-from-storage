# How Data Lake Handles One Line Insert?

Welcome to the second chapter of Learn Data Lake From Storage! In this chapter, we will explore how data lakes handle one line insert. Please make sure you have finished the setup work for data lakes you want to explore.

## Apache Hive

> Visit [Apache Hive](lakes/apache-hive) to get it setup.

Let's insert some data:

```sql
INSERT INTO example_table VALUES (1, "a");
```

We will see:

```shell
:) sudo tree /var/lib/docker/volumes/apache-hive_warehouse/_data
/var/lib/docker/volumes/apache-hive_warehouse/_data
└── example_table
    └── 000000_0

2 directories, 1 file
```

A file is created for the data. The file name is generated by hive, and the content is parquet format.

Let's use `parquet_tools` to inspect it:

```shell
:) sudo parquet-tools show /var/lib/docker/volumes/apache-hive_warehouse/_data/example_table/000000_0

+------+--------+
|   id | name   |
|------+--------|
|    1 | a      |
+------+--------+

:) sudo parquet-tools inspect /var/lib/docker/volumes/apache-hive_warehouse/_data/example_table/000000_0

############ file meta data ############
created_by: parquet-mr version 1.13.1 (build db4183109d5b734ec5930d870cdae161e408ddba)
num_columns: 2
num_rows: 1
num_row_groups: 1
format_version: 1.0
serialized_size: 415


############ Columns ############
id
name

############ Column(id) ############
name: id
path: id
max_definition_level: 1
max_repetition_level: 0
physical_type: INT32
logical_type: None
converted_type (legacy): NONE
compression: UNCOMPRESSED (space_saved: 0%)

############ Column(name) ############
name: name
path: name
max_definition_level: 1
max_repetition_level: 0
physical_type: BYTE_ARRAY
logical_type: String
converted_type (legacy): UTF8
compression: UNCOMPRESSED (space_saved: 0%)
```

So hive only store data files in the storage layer, and all the metadata are stored in the hive metastore.

## Apache Iceberg

> Visit [Apache Iceberg](lakes/apache-iceberg) to get it setup.

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

NOT FINISHED.