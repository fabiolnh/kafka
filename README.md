# Kafka Study

- An Open-Source distributed data stream plataform used for high performance in data pipeline, analytics stream, data integration and mission critical applications
- The Event World: Almost everything that we do, generates an Event (order an Uber, open a car door, a purchase, a transaction, etc)
- It was developed in Java. Born in Linkedin
- Good use for 2 contexts: Kafka is not a solution that has to be implements only in companies that have high volumetry of data. It can be implemented in companies that have a lot of services comunicating with each other.
- It works in cluster

## Questions:
  - Where do we have to save the event?
  - How to recover the feedback as fast as possible between two systems?
  - How we scale?
  - How we can have resiliancy and high availability? (systems cannot lose messages)
 
## Advantages:
 - High throughput (capacity of receive and process requests): it can handle milions requests per second
 - Latency is extremely low (2ms)
 - Scalable
 - Storage: It has a database area that keeps al the data. It is optimized and secure
 - High Availability
 - It connects with almost everything
 - Libraries for several technologies
 - Open Source tools: Kafka Connect, etc.
 - Big Companies using it. Banks Love it (a lot of transactions).

## How it works and important things:
 - Producer (Event) -> Kafka -> Brokers (Cluster Nodes) <- Consumer
 - Each broker has a data base
 - Kafka does not distribute messages. The Consumer is responsable for get it there.
 - Minimal recomendation for production: 3 brokers (3 nodes, 3 machines)
 - The brokers communicate with each other
 - Zookeper: it a software that helps the brokers to communicate with each other. However, Kafka is acquiring its own independency and probably it will not be used in the future

## Differences between Kafka and RabbitMQ
 - Kafka is different than RabbitMQ. RabbitMQ is Queue. Kafka is Topic
 - A Topic can be read by a lot of systems (a lot of systems can read the same topic). A Queue deliver the message to a unique consumer (once the message is delivered, the message is gone of the queue).
 - Kafka storages the messages (rabbitMQ works by default only with memory)

## Topics
  - A communicate channel responsible for receiving and providing sent data for the kafka
  - Message is composed by:
  ```
  1) Header: Metatada, it's not mandatory
  2) Key: Message Type
  3) Value: Content (json)
  4) Timestamp
  ```
## Partitions
  - A way to scale the topic. The message is proccessed faster because of the number of brokers. Each Topic can have one or more partitions to ensure the distribution and resiliency os its data. The information become more distributed. It is used to Kafka scale the topic
  - Topic -> Partitions (each one  may be in a different broker (node))
  - Collateral effect: how we assure the order of the messages? Two messages that has a order has to go to the same partition! To do it, we have to define the "key" of the message. Ex: Key "MoneyMovement", has a "transfer" and "cash back". Transfer has to be first than the cash back. So, to guarantee the order, we use the same key for the message. If the key is not informed, the messages will be sent to the partitions dinamically.
  - Replicatior Factor: Ex: If selected 2 Replicator Factors, It will have 2 copies of each partition suming all broker. It is used to have a backup. Helo to guarantee resiliency. Usually used 2 or 3 (3 is usually for very critical systems). Ex of 2 Replication Factors:
  ```
  Broker 1: Partition 1, Partition 3
  Broker 2: Partition 1, Partition 2
  Broker 3: Partition 2, Partition 3
  ```
  - Partition Leadership: The consumer will always read the topic from the partition leader.
  - Partition Follower: When the partition leader drops, the consumer will get the information from the follower (we do not set it, kafka sets with its algoritm)

## Delivery Guarantee
  - Ack 0 (None): When we are OK that some messages cannot be delivered, we use FF (Fire and Forget) (This way is fast because we do not neet a confirmation that the message was delivered into the broker)
  - Ack 1 (Leader): The leader saves the message and send an answer to the producer that the message was delivered (it is slower). However, if the Leader Broker lost its message (some bug), and did not sent it to the follower brokers, you receive a confirmation and you are going to think that the message was not lost
  - Ack -1 (ALL): the producer will send the message to the leader, the leader saves, the leader replicates to the followers, the followers send a confirmation to the leader that the messages was saved and the broker leader send a confirmation to the consumer (much more slower)
  
  Configurations:
  - At most once: Better performance. Some messages can be lost
  - at least once: Moderate performance. Can duplicate Messages (the worry is with the consumer that when he read some duplicated messages, he has to ignore)
  - Exactly once: Worst performance. Exactly one time. (the kafka guarantee that wil never have a duplicated message and will not duplicate anyone)

## Idempotent Producer
  - a producer configuration mode
  - When there is a Idempotent producer, kafka can check if there is duplicated messages (caused probably for some network issues or other problem), and the kafka can discard the duplicated message, besides the kafka can guarantee the order of the messages
  - if this configuration is set, the Ack configuration has to be "ALL"

## Consumer Groups
  - defined at the consumer
  - it is for better performance
  - Used in more than one consumer (the same software), however, working on different machines or in different proccesses
  - when the consumers are inside a consumer group (something defined), some partitions will be used for one consumer and other partitions for another consumer
  - when there is only one consumer (outside the consumer group or only one inside a consumer group), it will read all the partitions aleatory. There will be no separation.
  - Inside the same group, will be one consumer per partition. If there is more consumers than partitions, the last consumers will be idle.

## Commands
  - run "docker-compose up" and go inside de bash of the kafka "docker exec -it kafka_kafka_q bash"
  - Create a Topic: "kafka-topics --create --topic=teste --bootstrap-server=localhost:9092 --partitions=3"
  - List Topics: "kafka-topics --list --bootstrap-server=localhost:9092"
  - Consuming a Topic: "kafka-console-consumer --bootstrap-server=localhost:9092 --topic=teste"
  - Procude a message: "kafka-console-producer --bootstrap-server=localhost:9092 --topic=teste"
  - Consuming all the messages, not only the new ones (remember: if there is more than one partition and the producer didn´t send the messages with a key, the messages will appear without ordering. Because they are coming from more than one partition. The only way to ensure the orther is the producer send the "Key" to all the messages go to the same partition): "kafka-console-consumer --bootstrap-server=localhost:9092 --topic=teste --from-beginning" 
  - Consuming a Topic in the same consumer group (if the producer send the message, only one consumer will receive each message, because each partition will be read by one consumer, and the message will go to only one partition, aleatory): "kafka-console-consumer --bootstrap-server=localhost:9092 --topic=teste -group=x"
  - run "control center": localhost:9021

## OBS
  - For consumers and producers they have to install the library librdkafka (https://github.com/edenhill/librdkafka). It´s a C/C++ library that communicate with your own language library (there is an interface between them).
  - Consumer and Producer params: https://github.com/edenhill/librdkafka/blob/master/CONFIGURATION.md

## Kafka Connect
  - It´s a Kafka component that works as a centralized hub for simple integrations between databases, key-value stores, search indexes and file systems
  - It´s is very simples. It does not need do develop anything.
  - Connectors:
    * Data Source: From where (Ex: MySQL, Mongo, SalesForce)
    * Sink: Target (Ex: JDBC, Elasticsearch, AWS Lambda)
  - It uses a topic between the connections
  - While kafka there are "brokers" (a node), in kafka connect there are "workers" (nodes)
  - Standalone Worker: (one node)
    * Structure: (Worker(Connector(Task)))
    * Each Worker can have more than one connector
  - Distributed Workers: (more than one node)
    * The workers help each other to executes the tasks. They slice the tasks
    * The workers has to be in the same group (the same group id). It is very similar with the consumer group
    * Structure: 
      - Worker1(ConnectorA(Task1 - reading partition 1)
      - Worker2(ConnectorA(Task2 - reading partition 2)
      - Worker3(ConnectorA(Task3 - reading partition 3)
  - Converters:
    * The tasks use the converters to change the data format for the read and write in kafka.
    * Ex: Avro, Protobuf (GRPC), JsonSchema, Json, String, ByteArray
  - Dead Letter Queue (DLQ):
    * Sometimes we get an data but it is not in the exatcly format that we want causing some error (or other error). DLQ is the local that we send the message to analyse later.
    * property: "errors.tolerance". This configuration is only applied for conectors of type "Sink"
      - Types:
        * NONE: the task fails instantly. The task stops.
        * ALL: errors are ignored and the process keeps going
        * errors.deadletterqueue.topic.name = topic_name: is has to be "ALL". Everytime that has an error, this error goes to a topic. Inside the topic, the message wil come with some headers. And inside these headers we can see the log and the explanation for the error.
  - Confluent Hub: https://www.confluent.io/hub/
  - Some documentation for connectors: https://debezium.io/documentation/reference/2.0/
  - Kafka connect is not an ETL. If the transformation is very complex, then you have to use another solution
