# NiFi + Kafka development lab

The goal of this lab are to:
- Read the NYC Taxi datasets from sockets with NiFi
- Validate and convert the datasets to Avro format
- Write the datasets to Kafka
- Filter the rides dataset with NiFi and write it to Kafka

## Usefull links

- [Kafka Quick Start doc](http://kafka.apache.org/21/documentation.html#quickstart)
- [NiFi Getting Started doc](https://nifi.apache.org/docs/nifi-docs/html/getting-started.html)

## Prerequisites

Before you can start the lab, you have to complete the NiFi+Kafka install from the [previous lab](../nifi-kafka-vm/README.md).

## Lab

1. Copy the [ece-spark](https://github.com/adaltas/ece-spark) repository to your VM (in `/tmp` for example)
2. Stream the datasets using the Python script
3. Develop the first dataflow that reads the records from the sockets, convert them to Avro and writes them to Kafka
