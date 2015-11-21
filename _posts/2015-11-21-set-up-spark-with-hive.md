---
layout: post
title: "Set up Spark with Hive"
date: 2015-11-21 20:04:34
categories: tutorial
tags: spark hive tutorial
---


# Spark

- Download spark(I used the pre-built version so no need to build)
- Edit `conf/slaves` to add a list of hosts
- Modify port related variable in `conf/spark-env.sh`
- [Build spark](http://spark.apache.org/docs/latest/building-spark.html) if necessary
-  Setup [passwordless ssh](http://askubuntu.com/questions/46930/how-can-i-set-up-password-less-ssh-login) and ensure spark directory(built) is accessble for the workers
- Run `./sbin/stop-all.sh` to start the cluster
- Run `./bin/spark-shell --master spark://ukko186:50511` to start a Scala shell


# Hive(without Spark)

- Hive depends on Hadoop, install Hadoop if necessary
  - Download [hadoop](http://mirror.netinch.com/pub/apache/hadoop/common/)
  - Setup `HADOOP_HOME` and `JAVA_HOME`(path to JRE)
- Download [Hive](http://mirror.netinch.com/pub/apache/hive/hive-1.2.1/)
- Follow the [installation page](https://cwiki.apache.org/confluence/display/Hive/AdminManual+Installation#AdminManualInstallation-HiveCLIandBeelineCLI)
- Set `hive.metastore.warehouse.dir` in `hive-site.xml` and `chmod g+w` in HDFS. More in *Running Hive* section in [Get started with Hive](https://cwiki.apache.org/confluence/display/Hive/GettingStarted#GettingStarted-RunningHiveServer2andBeeline)

You might not need the following for using Spark with Hive:

- Run `hiveserver2` to start a sserver and `beeline -u jdbc:hive2://localhost:10000` to open a sesscion
- If you see the error *Another instance of Derby may have already booted*, check [here](http://mail-archives.apache.org/mod_mbox/db-derby-user/201410.mbox/%3C544ABD0A.9000305@gmail.com%3E)
- [java.net.URISyntaxException: Relative path in absolute URI](http://stackoverflow.com/questions/27099898/java-net-urisyntaxexception-when-starting-hive)

# Using Spark with  Hive 

Spark seems to integrate well with Hive.

- No need for all processes of the Hive part. Maybe just `hive-site.xml`? Hadoop part necessary?
- Copy `$HIVE_HOME/conf/hive-site.xml` to `$SPARK_HOME/conf/hive-site.xml`
- Note: no need to run `hiveserver2` when using spark-shell

Remember to run this test:

    val sqlContext = new org.apache.spark.sql.hive.HiveContext(sc)

    sqlContext.sql("CREATE TABLE IF NOT EXISTS src (key INT, value STRING)")
    sqlContext.sql("LOAD DATA LOCAL INPATH 'examples/src/main/resources/kv1.txt' INTO TABLE src")

    // Queries are expressed in HiveQL
    sqlContext.sql("FROM src SELECT key, value").collect().foreach(println)
