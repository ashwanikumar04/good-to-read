# Implementing Delayed Message Processing in Apache Kafka

Delayed message processing is essential in distributed systems. This guide explores multiple strategies to implement this in Apache Kafka.

## Table of Contents

1. [Method 1: Delay Queue with Separate Topic](#method-1-delay-queue-with-separate-topic)
    - [Avoiding Looping of Messages](#avoiding-looping-of-messages)
    - [Handling Consumer Failures](#handling-consumer-failures-in-method-1)
2. [Method 2: Use Punctuate in Kafka Streams API](#method-2-use-punctuate-in-kafka-streams-api)
3. [Method 3: TTL and Log Compaction](#method-3-ttl-and-log-compaction)
4. [Method 4: External Scheduler](#method-4-external-scheduler)
5. [Illustrative Diagrams](#illustrative-diagrams)

---

## Method 1: Delay Queue with Separate Topic

### Overview

- Produce the message to a "delay" topic with a `scheduled_time`.
- A consumer reads messages from this topic and checks the `scheduled_time`.
- Once the time arrives, forward the message to the actual processing topic.

### Code Snippet for Basic Logic

```java
// Produce to delay topic
ProducerRecord<String, String> record = new ProducerRecord<>(delayTopic, key, value);
record.headers().add("scheduled_time", Longs.toByteArray(scheduledTime));
producer.send(record);

// Consumer Logic
while (true) {
    ConsumerRecords<String, String> records = consumer.poll(Duration.ofMillis(100));
    for (ConsumerRecord<String, String> record : records) {
        long scheduledTime = Longs.fromByteArray(getHeader(record, "scheduled_time"));
        if (System.currentTimeMillis() >= scheduledTime) {
            forwardToRealTopic(record);
        } else {
            // Re-queue logic here
        }
    }
}
```

### Avoiding Looping of Messages

#### Time-based Re-queueing

Store messages in a priority queue, sorted by `scheduled_time`. At intervals, flush this queue back to the delay topic.

```java
PriorityQueue<ScheduledMessage> delayQueue = new PriorityQueue<>();
...
// Inside the consumer loop
if (System.currentTimeMillis() >= scheduledTime) {
    forwardToRealTopic(record);
} else {
    delayQueue.add(new ScheduledMessage(scheduledTime, record));
}
```

### Handling Consumer Failures in Method 1

Consumer failures can cause message loss. Here are some code techniques to handle this:

#### Persistent Local Storage

```java
// Saving to disk
FileOutputStream fos = new FileOutputStream("delayQueue.ser");
ObjectOutputStream oos = new ObjectOutputStream(fos);
oos.writeObject(delayQueue);
oos.close();
fos.close();
```

#### Distributed Cache using Redis

```java
Jedis jedis = new Jedis("localhost");
// Inside the consumer loop
if (System.currentTimeMillis() >= scheduledTime) {
    forwardToRealTopic(record);
} else {
    jedis.zadd("delayQueue", scheduledTime, serialize(record));
}
```

---

## Method 2: Use Punctuate in Kafka Streams API

Use Kafka Streams' `punctuate` feature to periodically check a state store and then forward ready messages.

```java
// In your Processor class
@Override
public void punctuate(long timestamp) {
    KeyValueIterator<String, ScheduledMessage> iter = stateStore.all();
    while (iter.hasNext()) {
        KeyValue<String, ScheduledMessage> entry = iter.next();
        if (timestamp >= entry.value.getScheduledTime()) {
            context.forward(entry.key, entry.value.getMessage());
            stateStore.delete(entry.key);
        }
    }
}
```

---

## Method 3: TTL and Log Compaction

Produce messages with a unique key and a TTL. Enable log compaction on the topic. Consumers process messages once their TTL has expired.

```java
// Produce with TTL
ProducerRecord<String, String> record = new ProducerRecord<>(topic, key, value);
record.headers().add("TTL", Longs.toByteArray(ttl));
producer.send(record);

// Consumer Logic
long ttl = Longs.fromByteArray(getHeader(record, "TTL"));
if (System.currentTimeMillis() >= record.timestamp() + ttl) {
    // Process the message
}
```

---

## Method 4: External Scheduler

Use an external scheduler, e.g., Quartz, to trigger message processing in Kafka.

---

## Illustrative Diagrams

### Basic Delay Logic

```
+------------+          +----------+           +--------------+
|   Producer | ------>  | Delay    |  ------>  |  Consumer    |
|            |          | Topic    |           | (checks time)|
+------------+          +----------+           +--------------+
```

### Handling Failures with Redis Cache

```
+------------+          +----------+           +--------------+
|   Producer | ------>  | Delay    |  ------>  |  Consumer    |
|            |          | Topic    |           |  (can fail)  |
+------------+          +----------+           +------|-------+
                                                  |
                                                  v
                                              +-------+
                                              | Redis |
                                              +-------+
```
