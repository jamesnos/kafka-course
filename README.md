# kafka-course

## Starting Servers
```
zookeeper-server-start config/zookeeper.properties
kafka-server-start config/server.properties
```

## Init topics
```
kafka-topics --zookeeper 127.0.0.1:2181 --topic first_topic --create --partitions 3 --replication-factor 1
kafka-topics --zookeeper 127.0.0.1:2181 --list
kafka-topics --zookeeper 127.0.0.1:2181 --topic first_topic --describe
kafka-topics --zookeeper 127.0.0.1:2181 --topic first_topic --delete
```

## Producers
```
kafka-console-producer --broker-list 127.0.0.1:9092 --topic first_topic
> foo
kafka-console-producer --broker-list 127.0.0.1:9092 --topic first_topic --producer-property acks=all
```

## Consumer
```
 kafka-console-consumer --bootstrap-server 127.0.0.1:9092 --topic first_topic
 > new messages
 kafka-console-consumer --bootstrap-server 127.0.0.1:9092 --topic first_topic --from-beginning
 > all messages
 kafka-console-consumer --bootstrap-server 127.0.0.1:9092 --topic first_topic --group my-first-application
```

## Consumer Groups CLI
```
kafka-consumer-groups --bootstrap-server 127.0.0.1:9092 --list
kafka-consumer-groups --bootstrap-server 127.0.0.1:9092 --describe --group my-second-application
```

## Replay data from offset (reset offsets)
```
kafka-consumer-groups --bootstrap-server 127.0.0.1:9092 --group my-first-application --reset-offsets --to-earliest --execute --topic first_topic
or
kafka-consumer-groups --bootstrap-server 127.0.0.1:9092 --group my-first-application --reset-offsets --shift-by -2 --execute --topic first_topic
___
kafka-console-consumer --bootstrap-server 127.0.0.1:9092 --topic first_topic --group my-first-application
> should get all messages again
```

## Kafka UI
https://www.kafkatool.com/

