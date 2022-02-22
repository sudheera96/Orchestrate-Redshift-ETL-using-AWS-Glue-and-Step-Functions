# AWS SNS

* SNS = Simple Notification Service
* Sends one message to many receivers.
* The “event producer” only sends message to one SNS topic
*  As many “event receivers” (subscriptions) as we want to listen to the SNS topic notifications
*  Each subscriber to the topic will get all the messages (note: new feature to filter messages)
*  Up to 10,000,000 subscriptions per topic
*  100,000 topics limit
*  Subscribers can be:

   • SQS

   • HTTP / HTTPS (with delivery retries – how many times)

   • Lambda

   • Emails

   • SMS messages

   • Mobile Notifications

