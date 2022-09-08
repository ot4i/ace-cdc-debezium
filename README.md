AppConnect Enterprise Change Data Capture with Debezium
=======================================================
[Change Data Capture (CDC)](https://www.redhat.com/en/topics/integration/what-is-change-data-capture) has been around for a long time, and the only out-of-the-box method for doing it with IIB/ACE has been to use our DatabaseInput node with Trigger functionality within the Database.  This has the downside that the Trigger will be performed within the transaction boundary and hence may introduce a delay in the original transaction.
The preferred method of performing CDC for many is therefore to look at the database logs, which will happen on a separate thread (or even in a separate process) to the original transaction.
Whilst we're working on integrating this into the product for the future, I'd like to show you how it can be done with current versions using KafkaConnect and Debezium.

Debezium?
---------
[Debezium](https://debezium.io) is an Open Source CDC tool written in Java and built on top of Kafka. It has support for [many](https://debezium.io/documentation/reference/stable/connectors/index.html) databases and can be set up both within and outside a Kubernetes / OpenShift environment.

What you need
-------------
There are a few things I'm going to assume you have set up already, just because this would be a very long page if we did everything from scratch!

1. IBM Integration Bus v10 or IBM App Connect Enterprise. If you don't have one already, you can use the [Developer edition](https://www.ibm.com/docs/en/app-connect/12.0?topic=enterprise-download-ace-developer-edition-get-started).
1. A Database, one of the ones in the supported list above would be best.  The easiest one to use with Debezium if you're just playing is probably MySQL.
1. A Kafka cluster; I used [Strimzi](https://strimzi.io) in a small Kubernetes cluster, but standalone is fine.
1. A way to drive changes in the Database.  I used a small flow in ACE to make changes (driven by a Kafka message, though HTTP input with the flow exerciser is arguably an easier method). You could also use simple SQL in your chosen DBs command line tool.

Using Debezium to Connect your Database to Kafka
------------------------------------------------
We use a tool called KafkaConnect to do this for us.  If you're using a standalone Kafka instance, I'd suggest simply following the [Debezium tutorial](https://debezium.io/documentation/reference/1.9/tutorial.html), or if you're using Strimzi then follow [this blog](https://strimzi.io/blog/2020/01/27/deploying-debezium-with-kafkaconnector-resource/).

You now have CDC messages in a Kafka queue, let's read them!

Reading the CDC messages with IIB/ACE
-------------------------------------

