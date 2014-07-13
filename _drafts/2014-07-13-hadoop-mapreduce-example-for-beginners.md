---
layout: post
title: Hadoop MapReduce example for beginners
categories:
- blog
---

```bash
$ git clone https://github.com/baswenneker/hadoop-single-node-vagrant.git
$ cd hadoop-single-node-vagrant/
$ git clone https://github.com/baswenneker/hadoop-mapreduce-sametaste.git
$ vagrant up
$ vagrant ssh -- -l hduser
```

The password of `hduser` is *hduser*. Now cd into the shared folder `/vagrant/` and compile the hadoop-mapreduce-sametaste code.

```bash
$ cd /vagrant/hadoop-mapreduce-sametaste
$ sudo javac -classpath `yarn classpath` -d classes/ SameTasteJob.java SameTasteMapper.java SameTasteReducer.java 
```

This command compiles the .java files and stores the result in `/vagrant/hadoop-marpreduce-sametaste/classes` folder. Note that the compile class path is set to `\`yarn classpath\``. It’s just for convenience:

```bash
$ echo `yarn classpath`

```



sudo javac -classpath `yarn classpath` -d sametaste_classes2/ SameTasteJob.java SameTasteMapper.java SameTasteReducer.java 

sudo jar cvf sametaste.jar -C sametaste_classes2/ .

```bash
hadoop jar /vagrant/hadoop-mapreduce-sametaste/sametaste.jar com.baswenneker.SameTaste /tmp/testing/sametaste_in /tmp/testing/sametaste_out
```

hdfs dfs -rm -r /tmp/testing/sametaste_out

http://192.168.33.10:50070/logs/userlogs/

hdfs dfs -cat /tmp/testing/sametaste_out/part-r-00000