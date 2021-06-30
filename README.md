# General

## Deployment type

Docker

## Image

Based on: https://github.com/big-data-europe/docker-hadoop

## Licence

Apache License 2.0

## Version

3.3.0

## Description

The Apache Hadoop software library is a framework that allows for the distributed processing of large data sets across clusters of computers using simple programming models. It is designed to scale up from single servers to thousands of machines, each offering local computation and storage. Rather than rely on hardware to deliver high-availability, the library itself is designed to detect and handle failures at the application layer, so delivering a highly-available service on top of a cluster of computers, each of which may be prone to failures.

# Deployment

A. General example with Docker run:

Start the namenode:

```sh
docker run -d --rm \
        --name namenode \
        -e CLUSTER_NAME=test \
        --env-file $HOME/hdfs/hadoop.env \
        -p 9870:9870 \
        -p 9000:9000 \
        -v $HOME/hdfs/namenode:/hadoop/dfs/name \
        digitbrain/bb-hdfs:namenode-hadoop3.3.0-java11
```

Start the datanode:

> Don't forget to modify namenode-IP on SERVICE_PRECONDITION variable.

```sh
docker run -d --rm \
        --name datanode1 \
        -e SERVICE_PRECONDITION=<namenode-IP>:9870 \
        --env-file $HOME/hdfs/hadoop.env \
        -v $HOME/hdfs/datanode/1:/hadoop/dfs/data \
        digitbrain/bb-hdfs:datanode-hadoop3.3.0-java11
```

> If you use Docker run, first copy the hadoop.env in the correct location and modify the first line to your CORE_CONF_fs_defaultFS=hdfs://`<namenode-IP>`:9000 or use the docker-compose version instead.

B. General example with Docker Compose:

```sh
docker-compose up -d
```

## Parameters

|Name|Value|Description|
|-|-|-|
|Ports|`-p 9870:9870`<br/>`-p 9000:9000`|HDFS WebUI<br/>NameNode CoreService|
|Volumes|`-v $HOME/hdfs/namenode:/hadoop/dfs/name`<br/>`-v $HOME/hdfs/datanode/1:/hadoop/dfs/data`|Persist HDFS namenode data<br/>Persist HDFS datanode data|
|Env|`-e CLUSTER_NAME=test`|Define the cluster name|
|EnvFile|`--env-file $HOME/hdfs/hadoop.env`|HDFS env file|

<br>

The configuration parameters can be specified in the hadoop.env file or as environmental variables for specific services (e.g. namenode, datanode):

## Testing

Direct your browser at NameNode: [http://\<HOST\>:9870](http://<HOST>:9870)

Upload a test file:

```sh
docker exec -it datanode touch testfile
docker exec -it datanode hdfs dfs -put testfile /
```