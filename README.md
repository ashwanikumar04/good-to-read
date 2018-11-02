This is a list of articles/ blog posts for best practices, system designs etc.

<!-- TOC -->

- [System Design](#system-design)
    - [How to deliver messages exactly once on scale](#how-to-deliver-messages-exactly-once-on-scale)
    - [Scalable task queues](#scalable-task-queues)
    - [An Approach to Designing a Distributed, Fault-Tolerant, Horizontally Scalable Event Scheduler](#an-approach-to-designing-a-distributed-fault-tolerant-horizontally-scalable-event-scheduler)
- [Best Practices](#best-practices)
    - [Build](#build)

<!-- /TOC -->

## System Design

### How to deliver messages exactly once on scale

This article explains how to deliver billions of messages exactly once. On high level, in order to achieve exactly once guarantee, the messages are not consumed directly from the message cluster to which they are produced instead a middle de-duplication layer is used to consume these messages and this layer publishes messages which are not duplicate to another cluster which are later consumed.

> #### More detail is available [here](https://segment.com/blog/exactly-once-delivery/)

### Scalable task queues

This article explains a scalable architecture for task queues. A kafka cluster in front of a redis cluster is used to provide write safety.

> #### More detail is available [here](https://slack.engineering/scaling-slacks-job-queue-687222e9d100)


### An Approach to Designing a Distributed, Fault-Tolerant, Horizontally Scalable Event Scheduler

This article explains an approach for a scalable event scheduler using a home-grown application on top of cassandra.

> #### More detail is available [here](https://medium.com/walmartlabs/an-approach-to-designing-distributed-fault-tolerant-horizontally-scalable-event-scheduler-278c9c380637)

## Best Practices

### Build

Often, we need to build projects which require a specialized environment to build. Reproducing the build environment becomes a tedious job over time.
One of the better approach is to use docker container to build the projects.
This article explains how to use docker container to build a project.

> #### More detail is available 
- [here](https://mikulskibartosz.name/how-to-build-a-project-inside-a-docker-container-fd575058bf4a)
- [here](https://dzone.com/articles/maven-build-local-project-with-docker-why)

