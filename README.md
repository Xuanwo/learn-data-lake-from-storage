# Learn Data Lake From Storage

Hello everyone, welcome to Learn Data Lake From Storage! 

Data lakes are complex systems characterized by varying specifications, formats, and engines. However, the foundational element of all data lakes is the storage layer. Observing how they organize metadata and data on this layer, as well as their optimization strategies based on file design, provides clearer insights. From the storage layer perspective, we can fundamentally understand data more thoroughly. All engines built upon a data lake are essentially implementation details. 

This project seeks to explore various data lake projects by deploying them and analyzing their storage behaviors to gain insights into their functionality and design.

## Layout

This project is structured into various data lake projects, each with its own directory. Every project includes a `README.md` file that describes the project and provides deployment instructions.

- [Apache Hive](./apache-hive)
- [Apache Iceberg](./apache-iceberg)
- [Apache Paimon](./apache-paimon)
- [Delta Lake](./delta-lake)

## Setup

We will use some tools to observe and inspect the data lake projects, please make sure the following tools are installed.

- `docker` and `docker-compose`: We will use docker to deploy the data lake projects.
- `python` and `pip`: We will use python to run some tools.
- `java`: We will use java to run some tools.

Prepare the python environment for some useful tools.

```bash
python -m -venv venv
source ./venv/bin/activate
pip install parquet_tools
pip install s3cmd

curl https://downloads.apache.org/avro/avro-1.11.3/java/avro-tools-1.11.3.jar -O ./tools/avro-tools-1.11.3.jar
```
