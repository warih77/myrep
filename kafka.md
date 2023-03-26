# Kafka
## Reading messages from topics into a text file
```sh
./kafka-console-consumer.sh --bootstrap-server host1:9093, host2:9093, host3:9093 --consumer.config /kafka/consumer.properties --from-beginning --topic topic_name >> /path/to/file/messages.txt
```
consumer.properties:
```txt
security.protocol=SASL_PLAINTEXT
sasl.mechanism=PLAIN
group.id=test-consumer-group
sasl.jaas.config=org.apache.kafka.common.security.scram.ScramLoginModule required username=johndou password=qwerty;
```

## Write message to topic from a text file
```sh
./kafka-console-producer.sh --broker-list host1:9093,host2:9093,host3:9093 --topic eskd-scanning-events --producer.config=/kafka/producer.properties < /kafka/eskd-scanning-events.20211216.txt
```

## Increase the number of replicas for a given topic
Create increase-replication-factor.json and put this content in it:
```txt
{"version":1,
  "partitions":[
     {"topic":"topic_name","partition":0,"replicas":[0,1,2]},
     {"topic":"topic_name","partition":1,"replicas":[0,1,2]},
     {"topic":"topic_name","partition":2,"replicas":[0,1,2]}
  ]
}
```
Use the file with the --execute option of the kafka-reassign-partitions tool:
```sh
export KAFKA_ZOOKEEPER_CONNECT=host1:2181,host2:2181,host3:2181/kafka_cluster_name
kafka-reassign-partitions --zookeeper $KAFKA_ZOOKEEPER_CONNECT --reassignment-json-file increase-replication-factor.json --execute
```
Verify the replication factor with the kafka-topics tool:
```sh
kafka-topics --zookeeper $KAFKA_ZOOKEEPER_CONNECT --topic topic_name --describe
```

## Increase the number of partitions for a given topic
```sh
export KAFKA_ZOOKEEPER_CONNECT=host1:2181,host2:2181,host3:2181/kafka_cluster_name
kafka-topics.sh --zookeeper $KAFKA_ZOOKEEPER_CONNECT --alter --topic topic_name --partitions 6 
```

## List topics
```
kafka-topics.sh --zookeeper $KAFKA_ZOOKEEPER_CONNECT --list
```

## Create topic
```
kafka-topics.sh --zookeeper $KAFKA_ZOOKEEPER_CONNECT --topic "eskd-doc-delete" --create --if-not-exists --partitions 3 --replication-factor 2 --config compression.type=gzip --config retention.ms=2592000000
```

## Describe topic
```sh
kafka-topics --zookeeper $KAFKA_ZOOKEEPER_CONNECT --describe --topic topic_name
```

## Delete topic
```sh
kafka-topics.sh --zookeeper $KAFKA_ZOOKEEPER_CONNECT --delete --topic topic_name
```

## Delete all data from the topic
```sh
kafka-topics.sh --zookeeper $KAFKA_ZOOKEEPER_CONNECT --alter --topic topic_name --config retention.ms=1000
```
after execute command change retention.ms for topic topic_name to previous value

## List ACLs
```sh
kafka-acls.sh --authorizer-properties zookeeper.connect="host1:2181,host2:2181,host3:2181/kafka_cluster_name" --list
```

## Describe consumer group
```sh
./kafka-consumer-groups.sh --bootstrap-server host1:9093,host2:9093,host3:9093 --command-config /kafka/myconsumer.properties --describe --group test-consumer-group
```

## Delete consumer group
```sh
./kafka-consumer-groups.sh --bootstrap-server host1:9093,host2:9093,host3:9093 --command-config /kafka/myconsumer.properties --delete --group test-consumer-group
```

## Produce message to kafka topic
```sh
./kafka-console-producer --bootstrap-server host1:9093,host2:9093,host3:9093 --topic my-topic
```