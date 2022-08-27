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
  - a kafka configuration mode
  - When there is a Idempotent producer, kafka can check if there is duplicated messages (caused probably for some network issues or other problem), and the kafka can discard the duplicated message, besides the kafka can guarantee the order of the messages

## Consumer Groups
  - it is for better performance
  - Used in more than one consumer (the same software), however, working on different machines or in different proccesses
  - when the consumers are inside a consumer group (something defined), some partitions will be used for one consumer and other partitions for another consumer
  - when there is only one consumer (outside the consumer group or only one inside a consumer group), it will read all the partitions aleatory. There will be no separation.
  - Inside the same group, will be one consumer per partition. If there is more consumers than partitions, the last consumers will be idle.
