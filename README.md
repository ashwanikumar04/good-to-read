This is a list of articles for some of the real time problems faced by some of the big companies.
<!-- TOC -->

- [How to deliver messages exactly once on scale](#how-to-deliver-messages-exactly-once-on-scale)
- [Scalable task queues](#scalable-task-queues)

<!-- /TOC -->
## How to deliver messages exactly once on scale

This article explains how to deliver billions of messages exactly once. On high level, in order to achieve exactly once guarantee, the messages are not consumed directly from the message cluster to which they are produced instead a middle de-duplication layer is used to consume these messages and this layer publishes messages which are not duplicate to another cluster which are later consumed.

> ### More detail is available [here](https://segment.com/blog/exactly-once-delivery/)

## Scalable task queues

This article explains a scalable architecture for task queues. A kafka cluster in front of a redis cluster is used to provide write safety.

> ### More detail is available [here](https://slack.engineering/scaling-slacks-job-queue-687222e9d100)