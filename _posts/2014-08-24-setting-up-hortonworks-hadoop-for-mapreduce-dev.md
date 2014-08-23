---
layout: post
title: Setting up Hortonworks 'Hadoop' for Java MapReduce Development 
categories:
- blog
---
I've always used [Vagrant](http://www.vagrantup.com "Vagrant") and vanilla Hadoop to create a [virtual Hadoop development environment](http://baswenneker.github.io/blog/2014/07/09/setting-up-hadoop-using-vagrant.html "Setting up Hadoop using Vagrant") because I like a clean and easy to set up development stack. However, the Vagrant bootstrapping script stopped working every now and then and I was fed up with fixing it. I then switched to the Hortonworks Sandbox, which works consistently.

This is a hackers guide to setting up a Java development environment with minimal effort. 

## Setting up Hortonworks Sandbox
Setting up Hortonworks is easy as pie. First, you need a virtualization environment. I prefer the recommended platform: VirtualBox. Grab your download [here](https://www.virtualbox.org/wiki/Downloads "VirtualBox download page"). Now go download the Hortonworks Sandbox image from the [Hortonworks download page](http://hortonworks.com/products/hortonworks-sandbox/#install
"Hortonworks Sandbox Download").

<div class="img-center" markdown="1">
![Download and import the Hortonworks Sandbox](/assets/images/download_install_hw_sbox.png)
</div>

After downloading the image, fire up Virtualbox, go to _File > Import Appliance_ and follow the steps to import the image. See the [Sandbox download page](http://hortonworks.com/products/hortonworks-sandbox/#install
"Hortonworks Sandbox Download") for more in depth instructions.

## Testdriving Hortonworks
Now you're ready to start the sandbox:

<div class="img-center" markdown="1">
![Start the Hortonworks Sandbox](/assets/images/start_hw_sbox.png)
</div>

Alternatively, you can start it without GUI (headless mode):

{% highlight bash %}
$ VBoxHeadless --startvm "Hortonworks Sandbox 2.1" &
{% endhighlight %}
_Note that the sandbox version is 2.1 at the time of writing._


After booting the image you can ssh into the sandbox by running the following command from a terminal (remember the password of _root_ is _hadoop_.):
{% highlight bash %}
$ ssh root@127.0.0.1 -p 2222
{% endhighlight %}

Or just point your browser to the local Welcome page [http://127.0.0.1:8888/](http://127.0.0.1:8888/ "Hortonworks Sandbox Welcome Page") or the configuration page [http://127.0.0.1:8000/about/](http://127.0.0.1:8000/about/ "Hortonworks Sandbox Congiguration Page"). Voila, there's your Hadoop development environment!

## Setting up your Java development stack
Now we've got Hadoop running, but we want to do some Java MapReduce coding so I'll explain how I have set up my dev environment.

I do Java development on the host, not inside the sandbox. I'm using MacVim as my IDE. Additionally I have Eclipse installed in case I need some more advanced IDE features like refactoring, organizing imports etc. Because I use MacVIm most of the times I also compile from the commandline.

### Setup Java and Hadoop
First you need to grab a copy of the [Hadoop sources](http://hadoop.apache.org/releases.html#Download "Hadoop Download Page"). The source tarball contains the required JARs for compiling our MapReduce programs. At the time of writing the version is _2.4.1_.

After unpacking we need to add the following JARs to the classpath in order to compile _basic_ MapReduce code without errors. Use the following on OSX to add the JARs to the classpath:

{% highlight bash %}
$ export CLASSPATH=${CLASSPATH}:~/Development/hadoop-2.4.1/share/hadoop/tools/lib/commons-logging-1.1.3.jar:~/Development/hadoop-2.4.1/share/hadoop/common/hadoop-common-2.4.1.jar:/Users/bas/Development/hadoop-2.4.1/share/hadoop/mapreduce/hadoop-mapreduce-client-core-2.4.1.jar:~/Development/hadoop-2.4.1/share/hadoop/common/lib/hadoop-annotations-2.4.1.jar 
{% endhighlight %}

I use the following commands to compile Java MapReduce programs and to create a JAR:

{% highlight bash %}
$ javac -d classes/ *.java 
$ jar cvf <your jar name here>.jar -C classes/ <package name here> 
{% endhighlight %}

### Copying the JAR to the sandbox
To move the JAR from the host machine to the sandbox you need a Shared Folder. The sandbox virtual machine must be powered off in order to add a shared folder.

You can use the VirtualBox GUI to add a Shared Folder:

<div class="img-center" markdown="1">
![Start the Hortonworks Sandbox](/assets/images/add_shared_folder.png)
</div>

Or if you feel comfortable, just use the commandline ([more info here](https://www.virtualbox.org/manual/ch04.html#sharedfolders
 "VirtualBox Shared Folder reference")):

{% highlight bash %}
$ VBoxManage controlvm "Hortonworks Sandbox 2.1" poweroff
$ VBoxManage sharedfolder add "Hortonworks Sandbox 2.1" --name "Desktop" --hostpath "/Users/bas/Desktop" --automount
{% endhighlight %}

### Running the MapReduce JAR
The final steps in the dev process are described in [this](https://github.com/hortonworks/hadoop-tutorials/blob/master/Community/T09_Write_And_Run_Your_Own_MapReduce_Java_Program_Poll_Result_Analysis.md#copy-jar-to-hue-home-directory "Community Tutorial 09: Write and Run Your Own MapReduce Java Program - Poll Result Analysis Using Hadoop, Java and Eclipse") excellent tutorial from the Hortonworks community. It explains how to copy the JAR to the sandbox, how to upload data to HDFS and how to run the MapReduce program.
