# AWS SQS

Amazon SQS is a web services that gives you access to a message queue that can be used to store messages
while waiting for a computer to process them.

Amazon SQS is a distributed queue system that enables web service applications to quickly
and reliably queue messages that one component in the application generates to be consumed
by another component. A queue is a temporary repository for messages that are awaiting processing.

Using Amazon SQS, you can decouple the components of an application so they run independently, easing
message management between components.

Any component of a distributed application can store messages in the queue, messages can contain up to 256KB
of text in any format. Any component can later retrieve the messages programmatically using the Amazon
SQS API.

The queue acts as a buffer between the component producing and saving data, and the component
receiving the data for processing. This means the queue resolves issues that arise if the
producer is producing work faster than the consumer can process it, or if the producer or consumer
are only intermittently connected to the network.

## Queue Types

* Standard Queues (default)
* FIFO Queues (First-In-First-Out)

### Standard Queue

This lets you have a nearly unlimited number of transactions per second. Standard
queues guarantee that a message is delivered at least once. However, occasionally
(because of the highly-distributed architecture that allows high throughput), more than
one copy of a message might be delivered out of order. Standard queues provide best-effort ordering
which ensures that messages are generally delivered in the same order as they are sent.

### FIFO Queues

The FIFO queue complements the standard queue. The most important features of this queue type are
FIFO delivery and exactly-once processing: The order in which messages are sent and received is
strictly preserved and a message is delivered once and remains available until a consumer processes
and deletes it; duplicates are not introduced into the queue. FIFO queues also support message groups
that allow multiple ordered message groups within a single queue. FIFO queues are limited to 300 transactions
per second (TPS), but have all the capabilities of standard queues.

### SQS Visibility Timeout

* The visibility timeout is the amount of time that the message is invisible in the SQS
queue after a reader picks up that message. Provided the job is processed before the
visibility time out expires, the message will then be deleted from the queue. If the
job is not processed within that time, the message will become visible again and another
reader will process it. This could result in the same message being delivered twice.
* Default Visibility Timeout is 30 seconds
* Increase it if your task takes > 30 seconds
* Maximum is 12 hours

## SQS Key Facts

* SQS is pull-based, not pushed-based
* Messages are 256 KB in size
* Messages can be kept in the queue from 1 minute to 14 days
* Default retention period is 4 days
* SQS guarantees that your messages will be processed at least once

## SQS Long Polling

* Amazon SQS long polling is a way to retrieve messages from your Amazon SQS queues
* While the regular short polling returns immediately (even if the message queue being polled is empty),
long polling doesn't return a response until a message arrives in the message queue, or the long poll times out
* As such, long polling can save you money.

## SQS Exam Tips

* SQS is a distributed message queueing system
* Allows you to decouple the components of an application so that they are independent
* Pull based, not push based
* Standard Queues (default) - best effort ordering; message delivered at least one.
* FIFO Queues (First In First Out) - ordering strictly preserved, message delivered
once, no duplicates e.g. good for banking transactions which need to happen in strict order.
* Visibility Timeout
    * Default is 30 seconds - increase if task takes > 30 seconds to complete
    * Max 12 hours
* Short Polling - returned immediately even if no messages are in the queue
* Long Polling - polls the queue periodically and only returns a response when a
message in the queue or the timeout is reached

# Extra Materials - SQS Delay Queues & Managing Large Messages

## SQS Delay Queues - Postpone Delivery of New Messages

* Postpone delivery of new messages to a queue for a number of seconds
* Messages sent to the Delay Queue remain invisible to consumers for the duration
of the delay period
* Default delay is 0 seconds, maximum is 900
* For standard queues, changing the settings doesn't affect the delay of messages
already in the queue, only new messages
* For FIFO queues, this affects the delay of messages already in the queue

## When should you use a Delay Queue?

* Large distributed applications which may need to introduce a delay in processing
* You need to apply a delay to an entire queue of messages e.g. adding a delay of
a few seconds, to allow for updates to your sales and stock control databases before
sending a notification to a customer confirming an online transaction

## Managing Large SQS Messages

* For large SQS messages - 256KB up to 2GB in size
    * Use S3 to store the message
    * Use Amazon SQS Extended Client Library for Java to manage them
    * (you'll also need the  AWS SDK for Java) - provides an API for S3 bucket
    and object operations

Note you can't use the following for large messages:

* AWS CLI
* AWS Management Console / SQS Console
* SQS API
* Any other AWS SDK

## SQS Extended Client Library for Java

* Specify that messages are always stored in Amazon S3 OR only messages >256KB
* Send a message which references a message object stored in S3
* Get a message object from S3
* Delete a message object from S3

# Large SQS Messages - Exam Tips

SQS Delay Queue:

* Postpone delivery of new messages
* Messages in Delay Queue remain invisible for the duration of the delay period (0-900s [15mins])
* Large distributed applications which may need to introduce a delay in processing

Managing Large Messages in S3

* Store large messages 256KB-2GB
* AWS SDK for Java
* Amazon SQS Extended Client Library for Java
* Cannot use: AWS CLI, AQS/SQS management console, SQS API
