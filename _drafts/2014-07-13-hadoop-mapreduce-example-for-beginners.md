---
layout: post
title: Hadoop MapReduce example for beginners
categories:
- blog
---
I'm working on a set of MapReduce examples and this post talks you through the first one.

### The Story
John invited Ben and Anna for dinner and asks them the put up a list of meals they like. John joined the lists:

{% highlight bash %}
John likes Chips 
John likes Chicken
John likes Pizza
Ben likes Chicken
Ben likes Fish
Ben likes Pizza
Anna likes Vegetables
Anna likes Cheese
Anna likes Pizza
Anna likes Fish
{% endhighlight %}

Now John has to figure out what meal he'll cook.
That's not much of a problem because John is an absolute *Hadoop-Java-MapReduce* wizard. He knows a way to MapReduce the list into the following format:

{% highlight bash %}
John likes Chips 
John likes Chicken
John likes Pizza
Ben likes Chicken
Ben likes Fish
Ben likes Pizza
Anna likes Vegetables
Anna likes Cheese
Anna likes Pizza
Anna likes Fish
{% endhighlight %}

### Environment Setup

For this example we're using a single-node Hadoop virtual machine provisioned by Vagrant. For a walkthrough just read [Setting up Hadoop using Vagrant](http://baswenneker.github.io/blog/2014/07/09/setting-up-hadoop-using-vagrant.html "Setting up Hadoop using Vagrant"). If you don't care, just execute the following lines. 

{% highlight bash %}
$ git clone https://github.com/baswenneker/hadoop-single-node-vagrant.git
$ cd hadoop-single-node-vagrant/
$ git clone https://github.com/baswenneker/hadoop-mapreduce-sametaste.git
$ vagrant up
$ vagrant ssh -- -l hduser
{% endhighlight %}

The password of `hduser` is *hduser*. 
So now we have everything in place, let's fire up hadoop:

{% highlight bash %}
$ start-dfs.sh
$ start-yarn.sh
{% endhighlight %}

Now we create a directory on HDFS in which we place the input file.
{% highlight bash %}
$ hdfs dfs -mkdir -p /tmp/testing/sametaste_in
$ hdfs dfs -copyFromLocal /vagrant/hadoop-mapreduce-sametaste/tastes.txt /tmp/testing/sametaste_in
{% endhighlight %}

Now `cd` into the shared folder `/vagrant/` and compile the hadoop-mapreduce-sametaste code.

{% highlight bash %}
$ cd /vagrant/hadoop-mapreduce-sametaste
$ sudo javac -classpath `yarn classpath` -d classes/ *.java
Note: SameTasteJob.java uses or overrides a deprecated API.
Note: Recompile with -Xlint:deprecation for details.
{% endhighlight %}

This command compiles the .java files and stores the result in `/vagrant/hadoop-marpreduce-sametaste/classes` folder. Just ignore the message about deprications.
Note that the compile class path is set to `\`yarn classpath\``. It’s just for convenience, it holds most of the hadoop class folders:

{% highlight bash %}
$ echo `yarn classpath`
/usr/local/hadoop/etc/hadoop:/usr/local/hadoop/etc/hadoop:/usr/local/hadoop/etc/hadoop:/usr/local/hadoop/share/hadoop/common/lib/*:/usr/local/hadoop/share/hadoop/common/*:/usr/local/hadoop/share/hadoop/hdfs:/usr/local/hadoop/share/hadoop/hdfs/lib/*:/usr/local/hadoop/share/hadoop/hdfs/*:/usr/local/hadoop/share/hadoop/yarn/lib/*:/usr/local/hadoop/share/hadoop/yarn/*:/usr/local/hadoop/share/hadoop/mapreduce/lib/*:/usr/local/hadoop/share/hadoop/mapreduce/*:/contrib/capacity-scheduler/*.jar:/usr/local/hadoop/share/hadoop/yarn/*:/usr/local/hadoop/share/hadoop/yarn/lib/*
{% endhighlight %}

After compiling we create the jar file in `/vagrant/hadoop-mapreduce-sametaste/` like:

{% highlight bash %}
$ sudo jar cvf sametaste.jar -C classes/ com 
{% endhighlight %}

{% highlight bash %}
$ hadoop jar /vagrant/hadoop-mapreduce-sametaste/sametaste.jar com.baswenneker.SameTaste /tmp/testing/sametaste_in /tmp/testing/sametaste_out
{% endhighlight %}

{% highlight bash %}
$ hdfs dfs -cat /tmp/testing/sametaste_out/part-r-00000
14/07/13 16:04:22 WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
Cheese  Anna 
Chicken Ben John 
Chips   John 
Fish    Anna Ben 
Pizza   Anna Ben John 
Vegetables  Anna 
{% endhighlight %}



hdfs dfs -rm -r /tmp/testing/sametaste_out

http://192.168.33.10:50070/logs/userlogs/

hdfs dfs -cat /tmp/testing/sametaste_out/part-r-00000