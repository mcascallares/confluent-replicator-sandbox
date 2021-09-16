# Replicator Sandbox

Sandbox environment to easily test Confluent Replicator against a single Kafka cluster that acts as a source and destination


## Start Zookeeper and Kafka (optional)

```
docker-compose up -d
```


## Create sample topic

```
kafka-topics --bootstrap-server localhost:29092 \
    --create \
    --topic test-topic \
    --partitions 3 \
    --replication-factor 1
```


## Produce sample data

```
kafka-producer-perf-test --throughput 500 \
    --num-records 100000000 \
    --topic test-topic \
    --record-size 100 \
    --producer-props bootstrap.servers=localhost:29092
```


## Execution

```
<path_to_replicator>/replicator \
 --consumer.config ./replicator-consumer.properties \
 --producer.config ./replicator-producer.properties \
 --cluster.id my-replicator \
 --replication.config ./replicator.properties \
 --whitelist test-topic
```


## Validate data being replicated

```
kafka-topics --bootstrap-server localhost:29092 \
    --describe \
    --topic test-topic-replicated
```

```
kafka-console-consumer --bootstrap-server localhost:29092 \
    --topic test-topic-replicated \
    --from-beginning \
    --property print.key=true
```


## Test behavior when source topic does not exist

```
<path_to_replicator>/replicator \
 --consumer.config ./replicator-consumer.properties \
 --producer.config ./replicator-producer.properties \
 --cluster.id my-replicator \
 --replication.config ./replicator.properties \
 --whitelist topic-that-does-not-exist
```

## Disabling topic configuration sync

Setting topic.config.sync to false will disable the sync of topic configuration
from source to target topics.

```
<path_to_replicator>/replicator \
 --consumer.config ./replicator-consumer.properties \
 --producer.config ./replicator-producer.properties \
 --cluster.id my-replicator \
 --replication.config ./replicator.properties \
 --whitelist test-topic \
 --topic.config.sync false
```

