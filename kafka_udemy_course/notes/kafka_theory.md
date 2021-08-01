# Kafka theory

## Topics

- Topic is like a table in relational database.
- We can have many topics in kafka. Topic names should be unique within a kafka cluster.
- Each topic is split in to partitions. Each partition has an offset, that never resets(incremental). Every new message is written to the location pointed by the offset.
- Once a data is written to a partition, the data can't be altered(immutability).
- When we create a topic, we need to specify the number of partitions to start with.
- Partitions of a topic can be changed later.
- Within each partition, messages are ordered by their arrival time.
- Partitioning logic can be through a **partition key or random**.
- By default messages in the partition are retained for about a week. We can configure the retention time.
- Messages that are sent to kafka are usually very small(like GPS position of a vehicle or outputs from sensors in IOT devices)

## Brokers

- A kafka cluster is composed of many brokers(servers).
- Each broker in a cluster is identified by an ID.
- Each broker contains certain topic partitions.
- When we are connected to a broker, we are connected to the entire kafka cluster.
- Recommende minimum number of brokers is 3.

## Topic replication

- If we don't have replication, and when one of the broker is down, then all the partitions of topic that this broker owns will not be available to the consumers for consumption.

- Replication factor needs to be decided. Recommended value is 3.

- Replication of factor 2, means each partition of a topic is replicated twice.

- Since partitions have replicas, for each partition we have a **leader broker** that receives the messages and serves the consumers. All other brokers containing this partition replicas will be called as **ISR(In Sync Replicas)**(they will just sync with the leader on the incoming messages and the partition offset)

- Leader and ISR are decided by zookeeper.

## Producers

- Producer write data to the topics.
- Producer knows which broker (leader) and partition to write to.
- Producer can chose to receive acknowledgement of data writes.

  - acks=0: Don't wait for ACK
  - acks=1: Wait for leader ACK
  - acks=all: Wait for acks from leader and all the ISRs

- Producer can send a key with the message. Key can be a number or string.
- If key is absent, the message is sent to partition in a round robin fashion. If we send a key, then any message with the same key will go the same partition(but we don't know which partition, that's decided based on the key's hash). Think of it like key to partition mapping is present.
- Key to partition mapping is done using key hashing.
- Key to partition mapping could change when we increase or decrease the partition count.

### Custom Partitioning

- If a producer provides a partition number in the message record, use it.
- If a producer doesn’t provide a partition number, but it provides a key, choose a partition based on a hash value of the key.
- When no partition number or key is present, pick a partition in a round-robin fashion.

**NOTE**- The **default partitioner** uses the hash value of the key and the total number of partitions on a topic to determine the partition number. **If you increase a partition number, then the default partitioner will return different numbers evenly if you provide the same key**. By using a custom partitioner we can manage the key to the partition mapping logic.

## Consumers

- Read messages from the Kafka topics.
- Consumers are allocated 1 or many partitions of a topic and they automatically know which broker(leader) to read from .
- Consumers will know how to recover from failures.
- Data within each partition is read in order.
- Consumer groups - may consumers with same group id. With many consumers in a consumer group, and all reading from the same topic, the data in the topic's partition can be read in parallel.
- Suppose a topic has 4 partitions and 2 consumers in the consumer group. Each consumer in the group is assigned 2 partitions each.
- If we have more consumers than the partitions in a consumer group, then additional consumers will be idle. A partition can be alloted to only one consumer. But 1 consumer can have more than 1 partition assigned.

## Consumer offsets

- Kafka stores the offsets at which a consumer group was reading.
- Offsets are committed live in another topic called `__consumer_offsets`(think of it like a topic to store the metadata about other topics).
- When Consumer in a group has processed the data that it received, it will commit the offset.
- So that if a consumer within the group dies, newly created consumer will know where to start reading from.

- Committing offets are associated with delivery semantics(when to commit).
  - **Atmost once** - Commit as soon as the messages are received
  - **Atleast once**(usually preferred) - Committed after processing the messages. After reading the messages, if the consumer goes down, new consumer will start from the same offset since the offsets where not committed. But the downside is if the consumer reads messages in a batch and dies in the middle of its processing, even if we read the same message from the new consumer, our system should be idempotent(duplicate processing doesnot affect the system)
  - **Exactly once** - Kafka to Kafka workflows using kafka streams API. Kafka to external system like database is exactly once.

## Kafka Broker discovery

- Every kafka broker is also a bootstrap server.
- Each broker knows about all the other brokers, topics and partitions
- When we connect to any broker, the kafka client(producer or consumer) will make a metadata request. Connected broker will publish list of all brokers and their IP addresses to the kafka client.

## Zookeeper

- Manages brokers
- Performs leader election of partitions
- Sends notifications to Kafka in case of changes(broker addition, removal)
- Kafka can't work without zookeeper.
- Zookeeper is often used in odd number of instances(3, 5, 7)
- Zookeeper will have a leader(handles writes) and the rest are followers(handles reads)

- Kafka manages its metadata in zookeeper, but does not store consumer offsets

## Kafka Guarantees

- Messages are appended to partitions in the order in which they are received by the brokers.
- Consumers read the messages in the same order in which they are written in the partitions.
- If replication factor is N, then producers and consumers can tolerate N-1 broker failures(atleast one is available)

---

## References

- [Custom partitioning in Kafka](https://dzone.com/articles/custom-partitioner-in-kafka-lets-take-quick-tour)
