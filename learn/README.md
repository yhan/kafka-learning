## Setup

Create `data` folder in the root, and create subfolders:  
- kafka
- zookeeper

Start Zookeeper
```
zookeeper-server-start.bat .\config\zookeeper.properties
```

Start server
```
kafka-server-start.sh .\config\server.properties
```

## Play using console client
Create topic
```
kafka-topics --zookeeper 127.0.0.1:5555 --topic second_topic --create --partitions 6 --replication-factor 1
```

Describe a topic
```
kafka-topics --zookeeper 127.0.0.1:5555 --topic first_topic --describe
> Topic: first_topic      PartitionCount: 3       ReplicationFactor: 1    Configs: 
        Topic: first_topic      Partition: 0    Leader: 0       Replicas: 0     Isr: 0
        Topic: first_topic      Partition: 1    Leader: 0       Replicas: 0     Isr: 0
        Topic: first_topic      Partition: 2    Leader: 0       Replicas: 0     Isr: 0
```


Delete a topic
```
kafka-topics --zookeeper 127.0.0.1:5555 --topic second_topic --delete
```

List all topics:
```
kafka-topics --zookeeper 127.0.0.1:5555 --list
```

Write to non existing topic
```
PS C:\kafka_2.13-2.5.0\config> kafka-console-producer --broker-list 127.0.0.1:9092 --topic new_topic
>New topic no warning
[2020-05-31 12:55:54,063] WARN [Producer clientId=console-producer] Error while fetching metadata with correlation id 3 : {new_topic=LEADER_NOT_AVAILABLE} (org.apache.kafka.clients.NetworkClient)

```

Change default nb of partitions, `server.properties`:  
num.partitions=1


Listen to a topic
```
kafka-console-consumer --bootstrap-server 127.0.0.1:9092 --topic first_topic --from-beginning
```

Concurrent consumers group
```sh
# start n consumers, view only one consumer of group will receive a published message 
kafka-console-consumer --bootstrap-server 127.0.0.1:9092 --topic first_topic --group my-app
```

```sh
# Check lag = diff(log-end-offset, current-offset)
PS C:\kafka_2.13-2.5.0> kafka-consumer-groups --bootstrap-server localhost:9092 --describe --group my-app

Consumer group 'my-app' has no active members.

GROUP           TOPIC           PARTITION  CURRENT-OFFSET  LOG-END-OFFSET  LAG             CONSUMER-ID     HOST            CLIENT-ID
my-app          first_topic     0          8               12              4               -               -               -
my-app          first_topic     1          6               10              4               -               -               -
my-app          first_topic     2          10              14              4               -               -               -

## Will consume the lagged messages
kafka-console-consumer --bootstrap-server 127.0.0.1:9092 --topic first_topic --group my-app

## CURRENT-OFFSET,  LOG-END-OFFSET have no difference now
> kafka-consumer-groups --bootstrap-server localhost:9092 --describe --group my-app
GROUP           TOPIC           PARTITION  CURRENT-OFFSET  LOG-END-OFFSET  LAG             CONSUMER-ID     HOST            CLIENT-ID
my-app          first_topic     0          8               12              4               -               -               -
my-app          first_topic     1          6               10              4               -               -               -
my-app          first_topic     2          10              14              4               -               -               -

```