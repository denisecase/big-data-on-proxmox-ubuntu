# big-data-on-proxmox-ubuntu

> Setting up big data tools on an Ubuntu VM running on Proxmox

Allow a couple hours to complete downloads and installations.

## Main Big Data Tools

*	Apache [Kafka](https://kafka.apache.org/downloads) (pub/sub) – version 2.13 binary version – comes with Zookeeper
*	Apache Spark / Spark Streaming (processing/reads from Kafka and Flume), [3.0.1 for Hadoop 3.2 and later](https://spark.apache.org/downloads.html)
*	Apache [Beam](https://beam.apache.org/get-started/downloads/) (cross-platform, write once, process anywhere)

## Big Data Tools - Prerequisites

*	[Python 3](https://www.python.org/downloads/) ([Anaconda individual edition](https://www.anaconda.com/products/individual) if possible)
*	[OpenJDK 11](https://jdk.java.net/15/) (Scala/sbt requires 11) – [installation instructions](https://openjdk.java.net/install/)
*	Scala 2.13.4 (https://www.scala-lang.org/)
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
* Wait until the updates finish. Restart. Login.
* In the lower left of the Ubuntu desktop, click the nine dots. Scroll to second window if needed to find "Terminal".
* Click the "Terminal" app to begin installing software. (You should see a $ prompt.) Enter password as needed.
* sudo apt-get update
* sudo apt-get upgrade -y
*	sudo apt install software-properties-common
*	sudo apt update
* sudo apt-get install curl -y
*	sudo apt-get install openjdk-11-jdk -y
*	sudo apt install git -y
*	sudo apt install maven -y
*	Python3 (or Anaconda) - see [article](https://www.hostinger.com/tutorials/how-to-install-anaconda-on-ubuntu/)
    * cd /tmp
    * curl -O https://repo.anaconda.com/archive/Anaconda3-2020.11-Linux-x86_64.sh
    * sha256sum Anaconda3-2020.11-Linux-x86_64.sh
    * bash Anaconda*.sh
*	Scala via sbt (requires JDK 11)
    * wget https://www.scala-lang.org/files/archive/scala-2.13.4.deb
    * sudo dpkg -i scala*.deb
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

## Verify Installs

```Bash
java -version
scala -version
python -version
```

Note: Anaconda will be installed in /home/big-data-user/anaconda3.

## Upload to Proxmox

