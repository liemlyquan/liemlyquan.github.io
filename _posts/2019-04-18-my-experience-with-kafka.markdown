---
layout: post
title:  "My experience with Kafka"
date:   2019-04-18 07:45:00 +0700
tags: kafka
---
A year before this post, the usage of Kafka, or message queue in general, would sound ridiculous to me. I just thought to myself: "why do we have to add complexity to our system, instead of directly calling the API". However, after months of using that, I have started to realize how (mostly) wrong I am.
## Speed
Unlike API, which does not only include data transfer, but also data processing, Kafka have only data transferring. Thus, the speed is much higher. LinkedIn stated that they are processing more than 2 billion messages per day using Kafka. We, at much smaller, are also processing millions of message per day, with no problem. 
## Log retention and fault tolerant
Kafka allows log retention, which means that the messages would be in your system for a few days. This would be useful in the following case. For some reasons (e.g: deployment), you have to shut down one of your services, for some minutes, or hours. With API, after unsuccessful retries, the client will surely give up. Thus, the request is gone forever. With Kafka, it can restart from the last processed message so your service can start consuming the next messages without anything loss. One thing to note though, you should not totally depend on the log retention; the log retention can buy us some time but if the rate of consuming message is way below producing, then you should quickly find a way to scale the consuming process, or else the message would be lost
## Loose-coupling services
Imagine you have multiple services. When you update one service, you expect others to also be notified and do something on their side. With normal API call, what you could to do is calling each of them individually. However, some problems must be taken into account
- It would be very manual to add each service into the code. And, what if you were to add other some service later, you have to go back and add code to notify the update to new service
- How would you handle error here? Failure in one service could prevent the update to other services, and stop the whole system flow
With Kafka, the updated service just need to notify the new message to the topic, and each service will consume the new message individually
## Setup and maintenance 
It is fairly easy to implement Kafka. You can have it up and running using these two Docker images
```
https://hub.docker.com/r/wurstmeister/zookeeper
https://hub.docker.com/r/wurstmeister/kafka/
```
However, there would be some maintenance required during operation:
- You have multiple instances and multiple partitions for one consumer group. You have to ensure that the number of partition % number of instance == 0 so that the message could be evenly distributed among all instances
- Sometimes Kafka can go wrong. A restart or even recreation might be required. We were once having a problem on our server, where the Kafka transfer speed is really slow. A restart simply made the problem gone
### NOTE:
- Kafka is no silver bullet that can totally replace normal API call, there are cases where Kafka would not work well
  - Instant response required. For instance, when a user changes his account password, the user expects to see the change immediately, then it should be called purely using API. Please note that sometimes near real-time is acceptable. With the above case, even though the password should be changed immediately, it is acceptable to have an email notifying the password change a minutes later.
  - Kafka does not do any data processing; thus, the data should be validated before passing in, if possible. If not, then you have to do the same validation at several services that consume the data