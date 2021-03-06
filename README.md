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
*	Kafka binary (includes Zookeeper, [requires JDK 8 or 11](https://kafka.apache.org/documentation/#java)) - see this [article](https://www.digitalocean.com/community/tutorials/how-to-install-apache-kafka-on-ubuntu-18-04):
    * sudo useradd kafka -m
    * sudo passwd kafka
    * enter password for user kafka
    * sudo adduser kafka sudo
    * su -l kafka (logs in a kafka user)
    * pwd (should show /home/kafka)
    * mkdir ~/Downloads
    * curl "https://downloads.apache.org/kafka/2.6.0/kafka_2.13-2.6.0.tgz" -o ~/Downloads/kafka.tgz
    * mkdir ~/kafka && cd ~/kafka
    * tar -xvzf ~/Downloads/kafka.tgz --strip 1
    * Recommended: verify content in /home/kafka/kafka
    * In the VM, open Firefox browser and go to the [Digital Ocean article](https://www.digitalocean.com/community/tutorials/how-to-install-apache-kafka-on-ubuntu-18-04). 
    * use nano to edit kafka/config/server.properties as directed (copy & paste from the article)
    * create the unit file for zookeeper as directed (copy & paste from the article)
    * enable kafka service on server boot (port 9092) with:
    * sudo systemctl enable kafka
    * sudo systemctl start kafka (start the service)
    * Test the application as directed (commands also below for reference)
    * sudo journalctl -u kafka
    * Hit q to exit (debug issues until service starts successfully)
    * Remove kafka from sudo group and lock the kafka user password so it can't be used to login:
    * sudo deluser kafka sudo
    * sudo passwd kafka -l
    * Right-click the terminal icon to quit this session
*	Spark
    * sudo apt install spark 
    * quit the terminal and open a new one to continue
* OR Spark the old way. See https://phoenixnap.com/kb/install-spark-on-ubuntu
    * wget https://downloads.apache.org/spark/spark-3.0.1/spark-3.0.1-bin-hadoop3.2.tgz
    * tar xvf spark-*
    * sudo mv spark-3.0.1-bin-hadoop3.2   /opt/spark
    * echo "export SPARK_HOME=/opt/spark" >> ~/.profile
    * echo "export PATH=$PATH:$SPARK_HOME/bin:$SPARK_HOME/sbin" >> ~/.profile
    * source ~/.profile
* Install Python Tools
    *	pip install --upgrade pip
    *	pip install --upgrade virtualenv
    *	pip install --upgrade setuptools
*	Create a Python virtualenv and activate it
    * From your home/big-data-user folder:
    * mkdir beam
    * cd beam
    * virtualenv env
    * source env/bin/activate
*	in the virtual Python env, install Beam – see https://beam.apache.org/get-started/quickstart-py/
    * pip install apache-beam
    * pip install apache-beam[gcp]
    * pip install apache-beam[aws]     
    * pip install apache-beam[test]     
    * pip install apache-beam[docs]   

### Commands To Verify Installations

```Bash
java -version
javac -version
scala -version
python3 --version
git --version
```

Note: Anaconda will be installed in /home/big-data-user/anaconda3.

### Typical Ports Used

* 2181 - Zookeeper service
* 9092 - Kafka service
* 7077 - Spark service
* 8080 - View Spark web interface at <http://127.0.0.1:8080/>
* 8099 - Beam Spark runner
* 4040 - View Beam Spark runner web interface at <http://localhost:4040>

### Starting / Restarting Zookeeper and Kafka Services

To execute Kafka shell commands on the VM, login as user kafka.

Run Zookeeper Service (keep running, minimize terminal) - in new terminal:

```Bash
cd kafka
./bin/zookeeper-server-start.sh ./config/zookeeper.properties
```

Run Kafka Service  (keep running, minimize terminal) -  in new terminal:

```Bash
cd kafka
./bin/kafka-server-start.sh ./config/server.properties
```

### Work with Kafka Topics

Recommendation: do NOT name topics with dot (.) or underscore(\_) as they could collide. 

To work with topics, cd kafka and run commands such as the following. 

```Bash
./bin/kafka-topics.sh --list --zookeeper localhost:2181 
./bin/kafka-topics.sh --create --topic topic517 --zookeeper localhost:2181 --replication-factor 1 --partitions 1 
./bin/kafka-topics.sh --delete --topic topic517 --zookeeper localhost:2181 
echo "Hello, World" | ./bin/kafka-console-producer.sh --broker-list localhost:9092 --topic topic517.noon > /dev/null
./bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic topic517.noon --from-beginning
```

When you are done testing, press CTRL+C to stop the consumer script. 


### Making Kafka Easier with Scripts

To create scripts, cd into kafka folder and use touch to create a set of files. 
Then add "execute" priviledges to all your scripts. 
For example:

```Bash
touch start-z.sh
touch start-k.sh
touch list-topics.sh
touch create-topic.sh
touch delete-topic.sh
chmod +x *.sh
```

Edit each file to include the command provided above (or similar). 
You may want to add #!/bih/sh to the top line. 
Execute with short commands from the kafka folder:

```Bash
./start-z.sh
./start-k.sh
./list-topics.sh
./create-topic.sh
./delete-topic.sh
```

### Test Spark

Open a new terminal and run:

```bash
/opt/spark/sbin/start-master.sh
```

Open Firefox to [http://127.0.0.1:8080/](http://127.0.0.1:8080/)

### Test Beam

Open a new terminal in /beam directory and prepare and run a test:

```bash
mkdir env/inputs
mkdir env/outputs

wget http://shakespeare.mit.edu/romeo_juliet/full.html -O env/inputs/romeo-juliet.txt

python -m apache_beam.examples.wordcount --input env/inputs/romeo-juliet.txt --output env/outputs/romeo-juliet

ls env/outputs/romeo*
cat env/outputs/romeo*
```

### Logout

When done, at the $ prompt, type `gnome-session-quit`.

Back in VirtualBox, on the big-data-ubuntu VM, right-click / Close / ACPI Shutdown. 

### Move Configured VM to Proxmox

Follow instructions in this [article](https://www.itsfullofstars.de/2019/07/import-ova-as-proxmox-vm/).


## Set up VM on Proxmox

- Log in to proxmox
- Start the big data VM
- Open Console (see upper right)
- Log in to Ubuntu
- follow the process above, starting with `sudo apt-get update`
- Aanaconda will default to /home/username/anaconda3 
- Before testing Kafka, configure Zookeeper as above and start Zookeeper
- Start Kafka and create a topic to test
- Recommended - Step 6 — Install KafkaT (Optional) from [article](https://www.digitalocean.com/community/tutorials/how-to-install-apache-kafka-on-ubuntu-18-04)


```ubuntu
cd /home/kafka/kafka/bin
./zookeeper-shell.sh localhost:2181  # verify zookeeper starts
sudo systemctl start kafka
sudo journalctl -u kafka  # view the logs (& address any errors) hit q to quit viewing
./kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic TutorialTopic
```
