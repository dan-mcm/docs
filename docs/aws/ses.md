# Amazon SES (Simple Email Service)

This is a scalable and highly available email service designed to help marketing teams
and application developers send marketing, notification, and transactional emails to
their customers using a pay as you go model.

Can also be used to receive emails: incoming mails can be delivered automatically to an S3 bucket.

Incoming mails can be used to trigger Lambda functions and SNS notifications.

## Use Cases

* Automated emails
* Purchase Confirmations, shipping notifications, order status updates e.g.
mobile phone company needs to send automated confirmation email every time a
customer purchases pre-paid mobile phone minutes.
* marketing communications, advertisements, newsletters, special offers e.g.
online retail business that needs to let customers know about sales promotions and discounts

## SES -v- SNS

| SES | SNS     |
| :------------- | :------------- |
| Email messaging service     | Pub/Sub messaging service, formats include SMS, HTTP, SQS, email       |
| Can trigger Lambda function or SNS notification       | Can be used to trigger Lambda function       |
| Can be used for both incoming and outgoing email       | Can fan out messages to large number of recipients (replicate and push messages to multiple endpoints and formats)       |
| An email address is all that is required to start sending messages to a user       | Consumers must subscribe to a topic to receive the notifications       |

## Exam Tips

* Remember that SES is email only
* Can be used for incoming and outgoing mail
* No subscription based, you only need to know the email address
* SNS supports multiple formats (SMS, SQS, HTTP, email)
* Push notifications only
* Pub/Sub model: consumers must subscribe to a topic
* You can fan-out messages to large number of recipients (e.g. multiple clients each with their own SQS queue)
