# Kafka Connect Mongo DB (Source) - S3 (Sink)

This repository contains a sample setup for Kafka Connect from MongoDB to S3 using...

- Redpanda as Kafka Replacement
- Redpanda Console as _UI_ for Kafka
- Minio as S3 replacement

## Setup

```bash
docker-compose up -d kafka mongo mongo-setup minio console connect
```

Download connectors and place them in `connectors` folder

- https://www.confluent.io/hub/confluentinc/kafka-connect-s3/
- https://www.confluent.io/hub/mongodb/kafka-connect-mongodb

## Interacting

> **NOTE**: MongoDB databases and collections as well as S3 buckets must exist upfront. Create them using the corresponding tooling, e.g. the Minio console or the Mongo Shell (or MongoDB Compass).

Show available connector plugins

```bash
curl localhost:8083/connector-plugins
```

MongoDB Source Connector config

```json
{
  "name": "mongo-simple-source",
  "connector.class": "com.mongodb.kafka.connect.MongoSourceConnector",
  "connection.uri": "mongodb://mongo",
  "database": "foo",
  "collection": "bar",
  "errors.tolerance": "all",
  "offset.partition.name": "foo",
  "output.json.formatter": "com.mongodb.kafka.connect.source.json.formatter.SimplifiedJson"
}
```

S3 Sink Connector config

```json
{
  "name": "s3-sink",
  "connector.class": "io.confluent.connect.s3.S3SinkConnector",
  "storage.class": "io.confluent.connect.s3.storage.S3Storage",
  "format.class": "io.confluent.connect.s3.format.json.JsonFormat",
  "aws.access.key.id": "password123",
  "aws.secret.access.key": "password123",
  "input.data.format": "JSON",
  "output.data.format": "JSON",
  "s3.bucket.name": "data",
  "time.interval": "HOURLY",
  "flush.size": "1",
  "tasks.max": "1",
  "topics": "foo.bar",
  "store.url": "http://minio:9000"
}
```

Sample `curl` to deploy connectors using the above configs stored as json files

```bash
curl --fail -X PUT localhost:8083/connectors/mongo-simple-source/config -H "Content-Type: application/json" --data-binary "@connector-configs/mongo-source.json"
```

> **NOTE**: See samples.json for some sample mongo documents

## Cleanup

```bash
docker-compose rm -v
```
