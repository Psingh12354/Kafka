# Apache Kafka

## Table of Contents

1. What is Kafka (Simple Explanation)
2. Why Do We Need Kafka?
3. Why Use Kafka? (Benefits)
4. How Kafka Works (Simple Diagram)
5. Core Concepts (Explained with Analogies)
6. Kafka Architecture (Post Office Example)
7. Producers & Consumers (Senders & Receivers)
8. Topics, Partitions & Offsets (Mailboxes)
9. How Kafka Handles Failures (Replication)
10. Message Delivery (At-most, At-least, Exactly Once)
11. How Data is Stored (Serialization & Schema)
12. Kafka Streams & Kafka Connect (Processing & Moving Data)
13. **Events vs Tasks** (New Section)
14. Security Basics (Locks & Keys)
15. Monitoring (How to Check Health)
16. Performance Tips (Simple Rules)
17. Setting up Kafka Locally with Docker
18. Code Examples (Java & Python)
19. Ecosystem (Friends of Kafka)
20. Dependent Topics (ZooKeeper vs KRaft)
21. Best Practices for Beginners
22. References

---

## 1. What is Kafka?

Kafka is a **software tool** that helps applications talk to each other using messages. Think of it as a **real-time mailbox system** for computers.

* An app can **send a message** (producer).
* Kafka **stores the message safely**.
* Another app can **read the message later** (consumer).

📦 **Example:**

* A shopping website sends an order event (`User bought shoes`).
* Kafka keeps it.
* Another app (billing) picks it up to generate an invoice.
* Another app (shipping) picks it up to arrange delivery.

![What is Kafka](https://miro.medium.com/v2/resize\:fit:1100/format\:webp/1*t2CUZ9Azl3Jk9P7ivd7C5Q.png)

---

## 2. Why Do We Need Kafka?

Without Kafka, apps would have to talk directly to each other. This creates **spaghetti connections** 🥴.

With Kafka, apps just drop messages into a mailbox. Any number of apps can read from that mailbox.

* **Without Kafka** → each app talks to all other apps.
* **With Kafka** → all apps talk only to Kafka.

![Why Kafka](https://miro.medium.com/v2/resize\:fit:1100/format\:webp/1*oU7pN4dLzN-Zg-7o2d6vHg.png)

---

## 3. Why Use Kafka? (Benefits)

* **High speed** 🚀 — Can handle millions of messages per second.
* **Durable** 💾 — Messages are stored on disk, so they don’t get lost.
* **Scalable** 📈 — Add more servers easily when traffic increases.
* **Fault-tolerant** 🔄 — Keeps copies of data, so even if one server fails, nothing is lost.
* **Flexible** 🔌 — Many apps can read the same data.
* **Real-time** ⏱️ — Data flows continuously with very low delay.

📦 **Example Benefits:**

* Netflix uses Kafka to track what people are watching (to recommend shows).
* Uber uses Kafka to match drivers and riders in real-time.
* Banks use Kafka to process payments instantly.

![Kafka Benefits](https://miro.medium.com/v2/resize\:fit:1100/format\:webp/1*4wGcKZ9VwRIFGB-YlW4pRQ.png)

---

## 4. How Kafka Works (Simple Diagram)

Here’s a **step-by-step visual** of how Kafka works:

![How Kafka Works](../mnt/data/A_2D_digital_illustration_diagram_titled_"How_Kafk.png)

* **Producer**: sends data (like letters to a mailbox).
* **Kafka Cluster**: stores data in partitions (drawers inside mailbox).
* **Consumer**: picks up data when needed.

---

## 5. Core Concepts

* **Broker** → Kafka server (post office branch).
* **Topic** → A named mailbox (e.g., `orders`, `payments`).
* **Partition** → Sub-sections of a mailbox for speed (like dividing work into multiple counters).
* **Offset** → Position of a message in a partition (like page number).
* **Producer** → Sender of messages.
* **Consumer** → Reader of messages.
* **Consumer Group** → A team of readers sharing work.

![Kafka Concepts](https://kafka.apache.org/24/images/kafka-apis.png)

---

## 6. Kafka Architecture (Post Office Example)

![Kafka Architecture](https://miro.medium.com/v2/resize\:fit:720/format\:webp/1*7COyRjA0kB1D9vlxQ51Tpw.png)

* Producers put letters in the post office.
* Kafka (post office) keeps letters safe.
* Consumers pick up their letters.
* Kafka ensures letters are not lost even if one branch (broker) fails.

---

## 7. Producers & Consumers

### Producer (Sender)

* Decides **which mailbox (topic)** to put the letter in.
* Can send with or without expecting delivery confirmation.

### Consumer (Reader)

* Reads letters from the mailbox.
* Can be one or many. If many, they can share the work.

📦 Example: Payment service sends `payment done`. Shipping service reads it.

![Producer and Consumer](https://kafka.apache.org/24/images/kafka-consumer-groups.png)

---

## 8. Topics, Partitions & Offsets

* **Topic** → Like a mailbox (`orders`).
* **Partitions** → Like dividing the mailbox into drawers. Each drawer has messages in order.
* **Offsets** → Each message has a number so consumers know where they left off.

📝 Example: Partition 0 has messages \[Order1, Order2], Partition 1 has \[Order3, Order4].

![Partitions](https://miro.medium.com/v2/resize\:fit:720/format\:webp/1*_l0KJeqtdz9vEtXGOBnVxA.png)

---

## 9. How Kafka Handles Failures

Kafka makes **copies** (replicas) of every drawer (partition).

* If one broker (server) fails, another copy is used.
* Typical: 3 copies of each partition.

![Replication](https://miro.medium.com/v2/resize\:fit:720/format\:webp/1*sE0pL02Af2c4xDPu5rYF2Q.png)

---

## 10. Message Delivery Types

* **At-most-once** → Send once, may be lost, never repeated.
* **At-least-once** → Guaranteed delivered, but could be duplicate.
* **Exactly-once** → Guaranteed delivered and no duplicates (hardest, but Kafka supports it).

![Delivery](https://miro.medium.com/v2/resize\:fit:720/format\:webp/1*X5xGZoKcX4m9JZZXx9UmHw.png)

---

## 11. How Data is Stored

* Kafka messages are just **bytes**.
* Apps must agree on format → JSON, Avro, Protobuf.
* Schema Registry → keeps track of message format so apps don’t break.

---

## 12. Kafka Streams & Kafka Connect

* **Kafka Streams**: A library to process data while it flows. Example: count how many orders per minute.
* **Kafka Connect**: Ready-made connectors to move data between Kafka and databases, cloud storage, etc.

![Streams and Connect](https://miro.medium.com/v2/resize\:fit:720/format\:webp/1*nSaPqcg8skZpMWxVtQ8wAw.png)

---

## 13. Events vs Tasks

A lot of beginners get confused between **events** and **tasks**. Let’s clear it up:

* **Event** → Something that already happened.

  * Example: *“User placed an order.”*
* **Task** → Something that needs to be done as a result of that event.

  * Example: *“Send confirmation email”* or *“Ship the order.”*

📦 Example Flow:

1. Event: *Order Created* → sent to Kafka.
2. Consumer A (Billing) → Task: *Generate invoice*.
3. Consumer B (Email Service) → Task: *Send confirmation email*.
4. Consumer C (Shipping Service) → Task: *Arrange delivery*.

👉 Kafka mainly stores and shares **events**. Consumers decide which **tasks** to perform.

![Event vs Task](https://miro.medium.com/v2/resize\:fit:720/format\:webp/1*L_RK0rCHzM6N9Vhmc6V7pg.png)

---

## 14. Security Basics

* **SSL/TLS**: Locking communication.
* **SASL**: Username/password to access.
* **ACLs**: Who can read/write which topic.

---

## 15. Monitoring

Things to watch:

* Consumer lag (is reader catching up?).
* Broker health (servers up?).
* Disk space (enough space for messages?).

---

## 16. Performance Tips

* More partitions → more parallelism.
* Use compression (snappy/gzip) to save space.
* Keep messages small (< 1 MB is good).

---

## 17. Local Setup with Docker

```yaml
version: '3.8'
services:
  zookeeper:
    image: confluentinc/cp-zookeeper:7.4.0
    environment:
      ZOOKEEPER_CLIENT_PORT: 2181
  kafka:
    image: confluentinc/cp-kafka:7.4.0
    ports:
      - 9092:9092
    environment:
      KAFKA_BROKER_ID: 1
      KAFKA_ZOOKEEPER_CONNECT: 'zookeeper:2181'
      KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://localhost:9092
      KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR: 1
```

Run:

```bash
# create topic
kafka-topics --create --topic orders --bootstrap-server localhost:9092 --partitions 1 --replication-factor 1

# send a message
kafka-console-producer --topic orders --bootstrap-server localhost:9092
> order123

# read message
kafka-console-consumer --topic orders --from-beginning --bootstrap-server localhost:9092
```

---

## 18. Code Examples

### Java Producer

```java
Properties p = new Properties();
p.put("bootstrap.servers", "localhost:9092");
p.put("key.serializer", "org.apache.kafka.common.serialization.StringSerializer");
p.put("value.serializer", "org.apache.kafka.common.serialization.StringSerializer");
Producer<String, String> producer = new KafkaProducer<>(p);
producer.send(new ProducerRecord<>("orders", "orderId", "123"));
producer.close();
```

### Python Consumer

```python
from confluent_kafka import Consumer

c = Consumer({
    'bootstrap.servers': 'localhost:9092',
    'group.id': 'mygroup',
    'auto.offset.reset': 'earliest'
})

c.subscribe(['orders'])
while True:
    msg = c.poll(1.0)
    if msg is None: continue
    print(msg.value().decode('utf-8'))
```

---

## 19. Ecosystem

* **Schema Registry** → define message format.
* **Kafka Connect** → move data in/out.
* **Kafka Streams** → process messages.
* **MirrorMaker** → copy messages across clusters.

---

## 20. Dependent Topics

* **ZooKeeper vs KRaft**: Old Kafka needed ZooKeeper. Newer Kafka uses KRaft (built-in system). Easier setup.
* **JVM**: Kafka runs on Java. Needs memory tuning.
* **Disk & Network**: Fast disks = faster Kafka.

---

## 21. Best Practices

* Always set replication factor ≥ 3.
* Don’t make too many partitions (start small).
* Monitor consumer lag.
* Secure your cluster.
* Use Schema Registry for structured messages.

---

## 22. References

* [Apache Kafka Official Docs](https://kafka.apache.org)
* [Confluent Kafka Documentation](https://docs.confluent.io)
* *Kafka: The Definitive Guide* (Book)

---

✨ With this, even if you’ve **never heard of Kafka before**, you now know it’s basically a **smart, fault-tolerant post office for data** where apps can send and receive messages safely.
