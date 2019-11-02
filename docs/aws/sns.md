# AWS SNS (Simple Notification Service)

Amazon SNS is a web service that makes it easy to set up, operate, and send notifications from the cloud.

* It provides developers with a highly scalable, flexible, and cost-effective capability to publish messages
from an application and immediately deliver them to subscribers or other applications.

* Examples: push notifications to Apple, google, Fire OS, and Windows, as well as Android devices in China
with Baidu Cloud Push.

* Besides pushing cloud notifications directly to mobile, SNS can also deliver notifications by
SMS text message or email to Amazon Simple Queue Service (SQS) queues, or to any HTTP endpoint

* SNS notifications can also trigger Lambda functions: When a message is published to an SNS topic
that has a Lambda function subscribed to it, the Lambda function is invoked with the payload of the
published message. The Lambda function receives the message payload as an input parameter and can
manipulate the information in the message, publish the message to another SNS topic, or send the message
to another AWS service.

* SNS allows you to group multiple recipients using topics. A topic is an "access point" for allowing recipients
to dynamically subscribe for identical copies of the same notification.

* One topic can support delivers to multiple endpoint types - for example, you can group together iOS, Android and
SMS recipients. When you publish once to a topic, SNS delivers appropriately formatted copies of your message
to each subscriber.

## SNS - Topics

To prevent messages from being lost, all messages published to Amazon SNS are stored redundantly
across multiple availability zones.

## SNS Benefits

* Instantaneous, push-based delivery (no polling)
* Simple APIs and easy integration with applications
* Flexible message delivery over multiple transport protocols
* Inexpensive, pay-as-you-go model with no up-front costs
* Web-based AWS management console offers the simplicity of a point-and-click interface

## SNS -v- SQS

* Both messaging Services in AWS
* SNS - push
* SQS - Polls (Pulls)

## SNS Pricing

* Users pay $0.50 per 1 million SNS requests
* $0.06 per 100,000 Notification delivers over HTTP
* $0.75 per 100 Notification delivers over SMS
* $2.00 per 100,000 Notification deliveries over Email

SNS follows the "publish-subscribe" (pub-sub) messaging paradigm, with notifications
being delivered to clients using a "push" mechanism that eliminates the need to periodically
check or "poll" for new information and updates.

With simple APIs requiring minimal up-front development effort, no maintenance or management overhead
and pay-as-you-go pricing, Amazon SNS gives developers an easy mechanism to incorporate a powerful
notification system with their applications.

## SNS Exam Tips

* SNS is a scalable and highly available notification service which allows you to send push
notifications from the cloud.
* Variety of message formats supported: SMS text message, email, Amazon Simple Queue Service (SQS) queues,
any HTTP endpoint.
* Pub-sub model whereby users subscribe to topics
* It is a push mechanism, rather than a pull (poll) mechanism
