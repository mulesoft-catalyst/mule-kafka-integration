# mule-kafka-integration
This asset aims to cover the following topics:

1. Setup of Kafka
2. Show MuleSoft to Kafka integration through a sample mule application using the out-of-the-box Apache Kafka Connector freely available on Anypoint Exchange

## Prerequsites

The following steps are required to have a running Kafka instance on-prem used to store our events

### Download the latest Kafka release

`https://kafka.apache.org/downloads`

### Extract it

```
$ tar -xzf kafka_2.13-2.6.0.tgz
$ cd kafka_2.13-2.6.0
```

### Start the Kafka environment

> NOTE: Your local environment must have OpenJDK 8 or 11 installed.

Run the following commands in order to start all services in the correct order:

```
# Start the ZooKeeper service
# Note: Soon, ZooKeeper will no longer be required by Apache Kafka.
$ bin/zookeeper-server-start.sh config/zookeeper.properties
```

Open another terminal session and run:

```
# Start the Kafka broker service
$ bin/kafka-server-start.sh config/server.properties

``` 

### Create a topic to store the events

Open a new terminal

`$ bin/kafka-topics.sh --create --topic <YOUR TOPIC NAME> --bootstrap-server localhost:9092`

## Mule application

The application implements the `Producer-Consumer pattern`. It's composed by two main flow:

1. The `Producer` flow is triggered by a POST invocation on the `localhost:8081/api/events` endpoint, containing in the body the message to store. i.e.
``` 
{
    "message" : "Message pushed into the queue"
}
``` 
2. The `Consumer` flow is triggered automatically by the `Message Listener` operation of the Apache Kafka Connector, and is used to retrieve the message from the queue and log it into the console.

### Application configuration

>NOTE: Be sure to have both the Kafka services running

1. Clone the repository and import the project into Anypoint Studio
2. Modify the field `kafka.topic` of the `test-properties.yaml` file (located in `src/main/resources`) in order to refernce `<YOUR TOPIC NAME>`

### Application test

1. Run the application
2. Push a message into the queue using a REST client (i.e. Postman, Advanced REST Client) by sending a POST containing the data to store to the `localhost:8081/api/events` endpoint, i.e.
``` 
{
    "message" : "Message pushed into the queue"
}
``` 
5. In Anypoint Studio log console you should find the event, retrieved from the queue, logged in the console i.e.
``` 
INFO  2020-11-30 15:00:00,000 [[MuleRuntime].uber.04: [mule-kafka-integration].consumerFlow.CPU_LITE @43b544] [processor: consumerFlow/processors/0; event: 9b716bc0-331a-11eb-aaba-3c22fb13cfba] org.mule.runtime.core.internal.processor.LoggerMessageProcessor: {
    "message" : "Message pushed into the queue"
}
``` 
