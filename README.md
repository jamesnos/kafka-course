# Kafka Course

## Install
```
Install binaries https://kafka.apache.org/downloads 2.6.0 Scala 2.13 
#export PATH="$PATH:/Users/jamesnos/kafka_2.13-2.6.0/bin"
or
brew install kafka
```

## Working Dir
```
cd /Users/jamesnos/kafka_2.13-2.6.0
```

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

## Kafka docker
https://github.com/simplesteph/kafka-stack-docker-compose

# AWS Amazon Kafka MSK
https://docs.aws.amazon.com/msk/latest/developerguide/create-topic.html

## Describe Cluster
```
aws kafka describe-cluster --region ap-southeast-1 --cluster-arn "arn:aws:kafka:ap-southeast-1:174778257743:cluster/test/a1c9d734-56b9-432a-8c43-49a9c7c90f07-2"

{
    "ClusterInfo": {
        "BrokerNodeGroupInfo": {
            "BrokerAZDistribution": "DEFAULT",
            "ClientSubnets": [
                "subnet-0955437479e561276",
                "subnet-08f6e22555912e81a"
            ],
            "InstanceType": "kafka.t3.small",
            "SecurityGroups": [
                "sg-04c5aaedcc769f989"
            ],
            "StorageInfo": {
                "EbsStorageInfo": {
                    "VolumeSize": 1000
                }
            }
        },
        "ClusterArn": "arn:aws:kafka:ap-southeast-1:174778257743:cluster/test/a1c9d734-56b9-432a-8c43-49a9c7c90f07-2",
        "ClusterName": "test",
        "CreationTime": "2020-10-24T04:46:05.246Z",
        "CurrentBrokerSoftwareInfo": {
            "KafkaVersion": "2.6.0"
        },
        "CurrentVersion": "K3P5ROKL5A1OLE",
        "EncryptionInfo": {
            "EncryptionAtRest": {
                "DataVolumeKMSKeyId": "arn:aws:kms:ap-southeast-1:174778257743:key/cfe07aca-8508-4b77-9913-f672ce998b63"
            },
            "EncryptionInTransit": {
                "ClientBroker": "TLS",
                "InCluster": true
            }
        },
        "EnhancedMonitoring": "DEFAULT",
        "NumberOfBrokerNodes": 2,
        "State": "ACTIVE",
        "Tags": {},
        "ZookeeperConnectString": "z-3.test.fw5mj3.c2.kafka.ap-southeast-1.amazonaws.com:2181,z-1.test.fw5mj3.c2.kafka.ap-southeast-1.amazonaws.com:2181,z-2.test.fw5mj3.c2.kafka.ap-southeast-1.amazonaws.com:2181"
    }
}
```

## Connect to Zookeeper
```
ZookeeperConnectString="z-3.test.fw5mj3.c2.kafka.ap-southeast-1.amazonaws.com:2181,z-1.test.fw5mj3.c2.kafka.ap-southeast-1.amazonaws.com:2181,z-2.test.fw5mj3.c2.kafka.ap-southeast-1.amazonaws.com:2181"
kafka-topics --create --zookeeper $ZookeeperConnectString --replication-factor 2 --partitions 1 --topic first_topic
kafka-topics --zookeeper $ZookeeperConnectString --list
```

## Produce / Consume
```
aws kafka get-bootstrap-brokers --region ap-southeast-1 --cluster-arn "arn:aws:kafka:ap-southeast-1:174778257743:cluster/test/a1c9d734-56b9-432a-8c43-49a9c7c90f07-2"

BrokerStringTls="b-2.test.fw5mj3.c2.kafka.ap-southeast-1.amazonaws.com:9094,b-1.test.fw5mj3.c2.kafka.ap-southeast-1.amazonaws.com:9094"

kafka-console-producer --broker-list $BrokerStringTls --topic first_topic
kafka-console-consumer --bootstrap-server $BrokerStringTls --topic first_topic --group my-first-application
```


# Python Library
https://github.com/dpkp/kafka-python

```
>>> from kafka import KafkaConsumer
>>> consumer = KafkaConsumer('first_topic', group_id='my_favorite_group', bootstrap_servers='127.0.0.1:9092')
>>> for msg in consumer:
...     print(msg)
...
ConsumerRecord(topic='first_topic', partition=2, offset=21, timestamp=1603524119363, timestamp_type=0, key=None, value=b'3d', headers=[], checksum=None, serialized_key_size=-1, serialized_value_size=2, serialized_header_size=-1)
```

Django Producer
```
class EventSerializer(serializers.Serializer):
    MESSAGE_TYPE = 'event'
    VERSION = 1
    KEY_FIELD = 'uuid'
    
    id = serializers.IntegerField()
    uuid = serializers.UUIDField()
    event_name = serializers.CharField(max_length=200)
    event_description = serializers.CharField(max_length=200)

class ProducerView(APIView):
    permission_classes = (AllowAny,)

    def get(self, request):
        uuid_id = uuid.uuid4()
        test = {'id': 386, 'uuid': uuid_id, 'event_name': 'new', 'event_description': 'new2'}
        producer = Producer('event', serializers.EventSerializer)
        producer.send(test)
        content = {'message': 'producer'}
        return Response(content)
```

Django consumer
```
from io import BytesIO

_delim = b':'
_formats = {}


def _bytes(seq):
    return seq.encode() if hasattr(seq, 'encode') else seq

def parse(data):
    data = _bytes(data)
    code, body = data.split(_delim, 1)
    # if code not in _formats:
    #     raise UnknownFormatError('Could not find parser for format %s' % code.decode())
    return body
    
class ConsumerView(APIView):
    permission_classes = (AllowAny,)

    def get(self, request):
        bootstrap_servers = ['b-2.mskcluster.3vjdvs.c2.kafka.ap-southeast-1.amazonaws.com:9092', 'b-1.mskcluster.3vjdvs.c2.kafka.ap-southeast-1.amazonaws.com:9092']

        consumer = KafkaConsumer(
            'event', 
            group_id='django-consumer2', 
            bootstrap_servers=bootstrap_servers, 
            consumer_timeout_ms=5000,
            auto_offset_reset='earliest', 
            enable_auto_commit=False
        )

        res = []
        for msg in consumer:
            data = parse(msg.value)
            res.append(data.decode('utf8'))

        content = {'message': res}
        return Response(content)
```


# PySpark Integration

# Setup notebook and pyspark locally
https://towardsdatascience.com/how-to-use-pyspark-on-your-computer-9c7180075617

### Start notebook with package
```
pyspark --packages org.apache.spark:spark-sql-kafka-0-10_2.12:3.0.0
spark-submit --packages org.apache.spark:spark-sql-kafka-0-10_2.12:3.0.0 spark-script.py
```

### Subscribe to topic
```
from pyspark.sql import SparkSession
from pyspark.sql.functions import explode
from pyspark.sql.functions import split

spark = SparkSession.builder.appName("Operations").getOrCreate()

lines = spark \
  .readStream \
  .format("kafka") \
  .option("kafka.bootstrap.servers", "192.168.1.77:9092") \
  .option("subscribe", "first_topic") \
  .load()


# Split the lines into words
words = lines.select(
   explode(
       split(lines.value, " ")
   ).alias("word")
)

# Generate running word count
wordCounts = words.groupBy("word").count()

query = wordCounts \
    .writeStream \
    .outputMode("complete") \
    .format("console") \
    .start()

query.awaitTermination()
```

https://kontext.tech/column/streaming-analytics/475/spark-structured-streaming-read-from-and-write-into-kafka-topics

### Start stand alone
```
/opt/spark-2.4.7/sbin/start-master.sh

/opt/spark-2.4.7/sbin/start-slave.sh spark://Jamess-MBP:8081
```
