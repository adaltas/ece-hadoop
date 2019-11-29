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
2. Stream the datasets to a socket using the Python script
3. Develop the first dataflow that reads the records from the sockets, convert them to Avro and writes them to Kafka
   1. Connect to NiFi: [http://20.20.20.21:8080/nifi](http://20.20.20.21:8080/nifi)
   2. Create a `GetTCP` processor to read data from the socket
      1. Properties: `Endpoint List = localhost:SOCKER_PORT`, `End of message delimiter byte = 10`
      2. Settings: `Automatically terminate relationships = Partial`
   3. Create a `UpdateAttribute` and a `ValidateRecord` processor
   4. Link the `GetTCP` `Success` relationship to the `UpdateAttribute` processor
   5. Start the `GetTCP` processor, FlowFiles should start queuing in the `Success` queue
   ![Dataflow v1](images/dataflow-v1.png)
   6. Add an attribute to the `UpdateAttribute` processor: `schema.name = nyc_taxi_fares`
   7. Go to `Configure` -> `CONTROLLER SERVICES` and:
      1. Create an `AvroSchemaRegistry`, and add the [`nyc_taxi_fares`](nyc_taxi_fares.avsc) schema (`nyc_taxi_fares = AVSC_FILE_CONTENT`)
      2. Create a `CSVReader` with the properties:
      ![CSVReader props](images/csv-reader-props.png)
      3. Create a `AvroRecordSetWriter` with default properties
      4. Enable all 3 Controller Services
      5. Reference the Controller Services in the `ValidateRecord` processor
   8. Add 2 `PublishKafkaRecord_2_0` processors
   9. Link the `valid` and `invalid` relationships of the `ValidateRecord` processor to the `PublishKafkaRecord_2_0` processors (1 by processor)
   ![Dataflow v2](images/dataflow-v2.png)
   10. Create 2 Kafka topics for fares records:
   ```
   /usr/kafka_2.11-2.1.0/bin/kafka-topics.sh --create --zookeeper localhost:2181 --partitions 2 --replication-factor 1 --topic nyc_taxi_fares_valid
   /usr/kafka_2.11-2.1.0/bin/kafka-topics.sh --create --zookeeper localhost:2181 --partitions 2 --replication-factor 1 --topic nyc_taxi_fares_invalid
   ```
   11. If you get an error, make sure Zookeeper and Kafka are started:
   ```
   sudo /usr/kafka_2.11-2.1.0/bin/zookeeper-server-start.sh -daemon /usr/kafka_2.11-2.1.0/config/zookeeper.properties
   sudo /usr/kafka_2.11-2.1.0/bin/kafka-server-start.sh -daemon /usr/kafka_2.11-2.1.0/config/server.properties
   ```
