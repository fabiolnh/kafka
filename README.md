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
