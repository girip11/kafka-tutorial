# Kafka Local setup

## Kafka local setup without docker

- Download and Setup Java 8 JDK using `sudo apt install openjdk-8-jdk`

- Download & Extract the Kafka binaries from [download kafka](https://kafka.apache.org/downloads). Extract and move the kafka folder to `/opt`

- Edit `PATH` to include Kafka (in `~/.bashrc` for example) `PATH="$PATH:/opt/kafka_2.XX-2.X.X/bin"`

- Try Kafka commands using bin/kafka-topics.sh (for example) and it should print some commandline usage to the console.

- Create `data/zookeeper` and `data/kafka` inside the `/opt/kafka_XXXX/` folder. Edit Zookeeper(`config/zookeeper.properties`) & Kafka(`config/server.properties`) configs found inside the `config` folder using a text editor

```conf
# zookeeper.properties
zookeeper.properties: dataDir=/your/path/to/data/zookeeper
```

```conf
#kafka server.properties under config
server.properties: log.dirs=/your/path/to/data/kafka
```

- Start Zookeeper in one terminal window `zookeeper-server-start.sh config/zookeeper.properties`

- Start Kafka in another terminal window `kafka-server-start.sh config/server.properties`

- Install conductkor kafka desktop UI from [here](https://www.conduktor.io/download/)

**NOTE**: For the rest of the course, don't forget to add the extension .sh to commands being run

## Setting up Kafka locally using docker

Two popular Kafka images are used.

- [Wurstmeister](https://github.com/wurstmeister/kafka-docker)
- [Confluent](https://hub.docker.com/r/confluentinc/cp-kafka/)
- [Bitnami image](https://bitnami.com/stack/kafka)

The following links would be useful to setup kafka based on wurstmeister images

- [Setting up a simple Kafka cluster with docker for testing](http://www.smartjava.org/content/setting-up-kafka-cluster-docker-copy/)
- [Sample docker compose](https://github.com/stockgeeks/docker-compose/blob/master/one-to-run-them-all/docker-compose.yml)
- [kafka docker image](https://github.com/wurstmeister/kafka-docker) [docs](http://wurstmeister.github.io/kafka-docker/)
- [Apache Kafka: Docker Container and examples in Python](https://towardsdatascience.com/kafka-docker-python-408baf0e1088)

Using confluent kafka docker images

- [Sample docker compose configuration](https://github.com/stockgeeks/docker-compose/blob/master/one-to-run-them-all/docker-compose-confluent.yml)

Bitnami tutorial

- [How to install Kafka using Docker](https://itnext.io/how-to-install-kafka-using-docker-a2b7c746cbdc)
