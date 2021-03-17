# Kafka CLI commands

## Kafka topics

- `kafka-topics.sh --help` for detailed usage of topic related arguments.

**NOTE** - When naming topics not to use `.` or `_`

**NOTE** - To do CRUD operations on kafka topics, we need to specify `--zookeeper`

- `kafka-topics.sh --zookeeper 127.0.0.1:2181 --topic first_topic --partitions 3 --replication-factor 1 --create --if-not-exists`

**NOTE**: Replication factor can never exceed the number of brokers.

- `kafka-topics.sh --zookeeper 127.0.0.1:2181 --list`

- `kafka-topics.sh --zookeeper 127.0.0.1:2181 --topic first-topic --describe` - Describe a topic

- `kafka-topics.sh --zookeeper 127.0.0.1:2181 --topic first-topic --delete` - Delete a topic. This will work only when kafka setting `delete.topic.enable` is set to true.

## Kafka CLI producer

- `kafka-console-producer.sh` is the script for this.

- `kafka-console-producer.sh --broker-list localhost:9092 --topic first-topic` - This command behaves like reading from standard input. Every line becomes a message. `Ctrl + c` to quit.

We can add producer properties like acknowledgement using `--producer-property` option.

- `kafka-console-producer.sh --broker-list localhost:9092 --topic first-topic --producer-property acks=all`

- When the producer tries to post to a topic that does not exist, the kafka server will create the topic with default settings for partitions and replication factor etc `kafka-topics.sh --zookeeper localhost:2181 --topic <new_topic> --describe`. **Posting to a non existent topic is never recommended**.

- Defaults for topic partition is available at `config/server.properties` with setting name `num.partitions`

- Configuration file change requires restarting kafka brokers locally. But seems like there are ways to set broker wise or cluster wise settings dynamically in kafka deployments in the cloud.

- `kafka-console-producer.sh --broker-list localhost:9092 --topic first-topic --property parse.key=true --property key.separator=,`

## Kafka consumer CLI

- `kafka-console-consumer.sh` script

- `kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic first-topic` - This consumer will start consuming those messages that reach kafka topic after the consumer has started.

- `kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic first-topic --from-beginning` - Fetches all the messages sent to the topic from the beginning.

- `kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic first-topic --from-beginning --property print.key=true --property key.separator=,`

## Kafka consumer group CLI

- `kafka-console-consumer.sh` will be used

- `kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic first-topic --group my-consumer-group` - This way we can create multiple consumers in the same consumer group.

- When using consumer group, offset per consumer group will be committed to kafka.

CRUD operations on consumer groups

- `kafka-consumer-groups.sh` script is used

- `kafka-consumer-groups.sh --bootstrap-server localhost:9092 --list`

**NOTE** - Console consumer without a consumer groups creates one implicitly with some random name.

- `kafka-consumer-groups.sh --bootstrap-server localhost:9092 --group <consumer_group_name> --describe` - This can be used to dump the offsets for each partition. Also we can find out how the partitions are distributed among the consumers of this consumer group.

- **LAG** - refers to what is the current offset of the consumer group vs the log offset to which the new messages will get written to.

### Resetting offsets

- `kafka-consumer-groups.sh` - script is used for resetting offsets

- `kafka-consumer-groups.sh --bootstrap-server localhost:9092 --group <con_group_name> --reset-offsets--to-earliest --execute --topic <topic_name>`

- `--to-earliest`, `--to-latest`(LAG will be 0), `--to-offser <offset_value>` and `--shift-by <pos_or_neg_value>` can be used to reset/move offsets.
