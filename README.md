# Kafka

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
