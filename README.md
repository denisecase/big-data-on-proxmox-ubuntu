# big-data-on-proxmox-ubuntu

> Setting up big data tools on an Ubuntu VM running on Proxmox

## Main Big Data Tools

*	Apache [Kafka](https://kafka.apache.org/downloads) (pub/sub) – version 2.13 binary version – comes with Zookeeper
*	Apache Spark / Spark Streaming (processing/reads from Kafka and Flume), [3.0.1 for Hadoop 3.2 and later](https://spark.apache.org/downloads.html)
*	Apache [Beam](https://beam.apache.org/get-started/downloads/) (cross-platform, write once, process anywhere)

## Big Data Tools - Prerequisites

*	[Python 3](https://www.python.org/downloads/) ([Anaconda individual edition](https://www.anaconda.com/products/individual) if possible)
*	[OpenJDK 11](https://jdk.java.net/15/) (Scala/sbt requires 11) – [installation instructions](https://openjdk.java.net/install/)
*	Scala 2.13.2 (https://www.scala-lang.org/)
*	Zookeeper coordination service – installed with Kafka above
*	HDFS (Hadoop distributed file system – might come with Spark?)
*	Git
*	Maven
* Curl (to install Anaconda 3)

## Set up Locally

*	Download and install Oracle VirtualBox 
* Download Ubuntu .iso ([18.04 LTS Desktop](https://releases.ubuntu.com/18.04/))
* [Install Ubuntu using VirtualBox](https://itsfoss.com/install-linux-in-virtualbox/)
  * Set VM name to: big-data-ubuntu
  * Set Hard disk type to VMDK
  * Select 'fixed', increase to ~40 GB
  * When installing Linux, create a general user, e.g., big-data-user
  * After post-install restart, if it pauses with an sda notice, hit enter until it continues. It may take awhile.
  * Do not install VirtualBox Guest Addons on Ubuntu (incompatible with Proxmox)
* After restart, login. Exit the wizards (or read and work through them). 
* Do not upgrade to Version 20, but accept updates for 18. 
* Open a terminal to begin installing software.
* sudo apt-get update
*	sudo apt install software-properties-common
*	sudo apt update
* sudo apt-get install curl
*	sudo apt-get install openjdk-11-jdk
*	sudo apt install git
*	sudo apt install maven
*	Python3 (or Anaconda if possible) 
*	Scala via sbt (requires OpenJDK 11) – see https://www.scala-lang.org/download/
*	Kafka (includes Zookeeper, requires OpenJDK) – see https://kafka.apache.org/documentation/#java – see https://www.digitalocean.com/community/tutorials/how-to-install-apache-kafka-on-ubuntu-18-04
*	Spark – see https://phoenixnap.com/kb/install-spark-on-ubuntu
*	pip install --upgrade pip
*	pip install --upgrade virtualenv
*	pip install --upgrade setuptools
*	create a python virtualenv 
*	in the virtual python env, install Beam – see https://beam.apache.org/get-started/quickstart-py/
* pip install apache-beam
* pip install apache-beam[gcp]
* pip install apache-beam[aws]     
* pip install apache-beam[test]     
* pip install apache-beam[docs]   

## Upload to Proxmox

