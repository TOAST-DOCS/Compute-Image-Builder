## Compute > Image Builder > Installation Component Guide

## PostgreSQL

### How to Start/Stop PostgreSQL

```
#Start the postgresql service
shell> systemctl start postgresql-${version}

#Stop the postgresql service
shell> systemctl stop postgresql-${version}

#Restart the postgresql service
shell> systemctl restart postgresql-${version}
```

### Connect to PostgreSQL

After creating an instance, initially connect as follows.
<br>
```
#Log in after switching the account to postgres
shell> su - postgres
shell> psql
```

### Initial Setup After Creating a PostgreSQL Instance

#### 1\. Change the Port

The provided image port is 5432, which is the PostgreSQL default port. For security reasons, it is recommended to change the port.
<br>
```
shell> vi /var/lib/pgsql/${version}/data/postgresql.conf


#Enter the port to use in the postgresql.conf file.

port =port name to use


#Save vi editor


#Restart the postgresql service

shell> systemctl restart postgresql-${version}


#Connect to the changed port as follows

shell> psql -p [changed port number]
```

#### 2\. Change the Server Log Time Zone

The default time zone logged in the server log is set to UTC. It is recommended to change it to be the same as the SYSTEM local time.
<br>
```
shell> vi /var/lib/pgsql/${version}/data/postgresql.conf


#Enter the time zone to use in the postgresql.conf file.

log_timezone =time zone to use


#Save vi editor


#Restart the postgresql service

shell> systemctl restart postgresql-${version}


#Connect to postgresql

shell> psql


#Confirm changed settings

postgres=# SHOW log_timezone;
```

#### 3\. Revoke the public Schema Privilege

By default, all users are granted CREATE and USAGE privileges for the public schema, so users who can access the database can create objects in the public schema. It is recommended to revoke the privileges to prevent all users from creating objects in the public schema.
<br>
```
#Connect to postgresql

shell> psql


#Execute the command to revoke the privilege

postgres=# REVOKE CREATE ON SCHEMA public FROM PUBLIC;
```

#### 4\. Allow Remote Access

To allow access from connections other than localhost, you need to change the listen_addresses variable and the client authentication configuration file.
<br>
```
shell> vi /var/lib/pgsql/${version}/data/postgresql.conf


#Specify the addresses to allow in the postgresql.conf file.
#0.0.0.0 to allow all IPv4 addresses
#:: to allow all IPv6 addresses
#* to allow any address

listen_addresses =Addresses to allow


#Save vi editor


shell> vi /var/lib/pgsql/${version}/data/pg_hba.conf


#Client authentication control by IP address format
#Older client libraries do not support the scram-sha-256 method, so it needs to be changed to md5

# TYPE  DATABASE        USER            ADDRESS                 METHOD
# IPv4 local connections:
host    all             all             127.0.0.1/32            scram-sha-256
host    Allowed DB          Allowed user         Allowed address                  scram-sha-256
# IPv6 local connections:
host    all             all             ::1/128                 scram-sha-256
host    Allowed DB          Allowed user         Allowed address                  scram-sha-256


#Restart the postgresql service

shell> systemctl restart postgresql-${version}
```

### PostgreSQL Directory Description

PostgreSQL directory and file descriptions are given below.

| Name | Description |
| --- | --- |
| postgresql.cnf | /var/lib/pgsql/{version}/data/postgresql.cnf |
| initdb.log | PostgreSQL database cluster creation log - /var/lib/pgsql/{version}/initdb.log |
| DATADIR | PostgreSQL data file path - /var/lib/pgsql/{version}/data/ |
| LOG | PostgreSQL log file path - /var/lib/pgsql/{version}/data/log/\*.log |

## MySQL

### How to Start/Stop MySQL

``` sh
# Start the MySQL service
shell> sudo systemctl start mysqld

# Stop the MySQL service
shell> sudo systemctl stop mysqld

# Restart the MySQL service
shell> sudo systemctl restart mysqld
```

### Connect to MySQL

After creating an instance, initially connect as follows.

``` sh
shell> mysql -u root
```

After changing the password, connect as follows.

``` sh
shell> mysql -u root -p
Enter password:
```

### Initial Setup After Creating a MySQL Instance

#### 1\. Setting Password

There's no password on root user on initial installation. Therefore, it is required to set password as soon as possible.

```
mysql> ALTER USER USER() IDENTIFIED BY 'NEW PASSWORD';
```

Default MySQL validate_password_policy is as below:

* validate\_password\_policy=MEDIUM
* Must be more than 8 characters, and include numbers, lower/upper cases, and special characters.


#### 2\. Change the Port

After initial installation, the port is 3306, which is MySQL's default port. For security reasons, it is recommended to change the port.

##### 1) Modify the `/etc/my.cnf` file

Specify the port you want to use in the `/etc/my.cnf` file.

```
shell> sudo vi /etc/my.cnf
```

```
port=[port address to change]
```

##### 2) Restart the instance
Restart the instance for the port change to take effect.
```
sudo systemctl restart mysqld
```

## MariaDB

### How to Start/Stop MariaDB

``` sh
# Start the MariaDB service
shell> sudo systemctl start mariadb.service

# Stop the MariaDB service
shell> sudo systemctl stop mariadb.service

# Restart the MariaDB service
shell> sudo systemctl restart mariadb.service
```

### Connect to MariaDB

After creating an instance, initially connect as follows.

``` sh
shell> sudo mysql -u root
```

After changing the password, connect as follows.

``` sh
shell> mysql -u root -p
Enter password:
```

### Initial Setup After Creating a MariaDB Instance

#### 1\. Set the Password

After initial installation, the MariaDB root account password is not set. Therefore, you must set a password after installation.

```
SET PASSWORD [FOR user] = password_option

MariaDB> SET PASSWORD = PASSWORD('password');
```

#### 2\. Change the Port

After initial installation, the port is 3306, which is MariaDB's default port. For security reasons, it is recommended to change the port.

##### 1) Modify the `/etc/my.cnf.d/servfer.cnf` file

Open the `/etc/my.cnf.d/server.cnf` file and enter the port address to change under [mariadb] as follows.

```
shell> sudo vi /etc/my.cnf.d/server.cnf
```

```
[mariadb]
port=[port address to change]
```

##### 2) Restart the instance
Restart the instance for the port change to take effect.
```
sudo systemctl restart mariadb.service
```

## CUBRID

### How to Start/Stop the CUBRID service

You can start or stop the CUBRID service as follows by logging in with the “cubrid” Linux account.

``` sh
# Start the CUBRID service/server
shell> sudo su - cubrid
shell> cubrid service start
shell> cubrid server start demodb

# Stop the CUBRID service/server
shell> sudo su - cubrid
shell> cubrid server stop demodb
shell> cubrid service stop

# Restart the CUBRID service/server
shell> sudo su - cubrid
shell> cubrid server restart demodb
shell> cubrid service restart

# Start/stop/restart the CUBRID broker
shell> sudo su - cubrid
shell> cubrid broker start
shell> cubrid broker stop
shell> cubrid broker restart
```

### Connect to CUBRID

After creating an instance, connect as follows.

``` sh
shell> sudo su - cubrid
shell> csql -u dba demodb@localhost
```

### Initial Setup After Creating a CUBRID Instance

#### 1\. Set the Password

After initial installation, the CUBRID dba account password is not set. Therefore, you must set a password after installation.

```
shell> csql -u dba -c "ALTER USER dba PASSWORD 'new_password'" demodb@localhost
```

#### 2\. Change the broker Port

The broker port for **query\_editor** defaults to **30000**, and the broker port for **broker1** defaults to **33000**.
For security reasons, it is recommended to change the port.

##### 1) Modify the broker file

Open the `/opt/cubrid/conf/cubrid\_broker.conf` file and enter the port address to change as follows.

```
shell> vi /opt/cubrid/conf/cubrid_broker.conf
```

```
[%query_editor]
BROKER_PORT             =[port address to change]

[%BROKER1]
BROKER_PORT             =[port address to change]
```

##### 2) Restart the broker
Restart the broker for the port change to take effect.
```
shell> cubrid broker restart
```

## Apache Kafka
> [Note]
> This guide is created based on Kafka version 3.3.1.
> If you are using a different version, please makes changes accordingly.
> For the instance flavor, please choose c1m2 (CPU 1core,  Memory 2GB) or higher specifications.

### Start and Stop Zookeeper, Kafka broker
```
# Start Zookeeper, Kafka broker (Zookeeper first)
shell> sudo systemctl start zookeeper.service
shell> sudo systemctl start kafka.service

# Stop Zookeeper, Kafka broker (Kafka broker first)
shell> sudo systemctl stop kafka.service
shell> sudo systemctl stop zookeeper.service

# Restart Zookeeper, Kafka broker
shell> sudo systemctl restart zookeeper.service
shell> sudo systemctl restart kafka.service
```

### Install Kafka Cluster
- Must install in a new instance.
- An odd number of instances (3 or more) are required, and the installation script is executed in the instance.
- An instance consists of of one kafka broker and one zookeeper node.
- The key pair (PEM file) required to connect to another instance must be located at the ~ path of the instance running the installation script. The key pair of cluster instances must be the same.
- Only default port installation is supported. If you need to change the port, change the port by referring to the initial settings guide after completing cluster installation.
- For Kafka-related port communication between instances, set security group as follows.

Set security group
```
Direction: Inbound
IP protocol: TCP
Port: 22, 9092, 2181, 2888, 3888
```
How to check Hostname and IP
```
# Check Hostname
shell> hostname
# Check IP
Console screen
or shell> hostname -i
```
Example of executing the cluster installation script (enter the hostname and IP checked above)
```
shell> sh ~/.kafka_make_cluster.sh

Enter Cluster Node Count: 3
### 3 is odd number.
Enter Cluster's IP ( Cluster 1 ) : 10.0.0.1
Enter Cluster's HOST_NAME ( Cluster 1 ) : kafka1.novalocal
Enter Cluster's IP ( Cluster 2 ) : 10.0.0.2
Enter Cluster's HOST_NAME ( Cluster 2 ) : kafka2.novalocal
Enter Cluster's IP ( Cluster 3 ) : 10.0.0.3
Enter Cluster's HOST_NAME ( Cluster 3 ) : kafka3.novalocal
10.0.0.1 kafka1.novalocal
10.0.0.2 kafka2.novalocal
10.0.0.3 kafka3.novalocal
Check Cluster Node Info (y/n) y
Enter Pemkey's name: kafka.pem
ls: cannot access /tmp/kafka-logs: No such file or directory
ls: cannot access /tmp/zookeeper: No such file or directory
### kafka1.novalocal ( 10.0.0.1 ), Check if kafka is being used
### kafka1.novalocal ( 10.0.0.1 ), Store node information in the /etc/hosts directory.
### kafka1.novalocal ( 10.0.0.1 ), Modify zookeeper.properties.
### kafka1.novalocal ( 10.0.0.1 ), Modify server.properties.
ls: cannot access /tmp/kafka-logs: No such file or directory
ls: cannot access /tmp/zookeeper: No such file or directory
### kafka2.novalocal ( 10.0.0.2 ), Check if kafka is being used
### kafka2.novalocal ( 10.0.0.2 ), Store node information in the /etc/hosts directory.
### kafka2.novalocal ( 10.0.0.2 ), Modify zookeeper.properties.
### kafka2.novalocal ( 10.0.0.2 ), Modify server.properties.
ls: cannot access /tmp/kafka-logs: No such file or directory
ls: cannot access /tmp/zookeeper: No such file or directory
### kafka3.novalocal ( 10.0.0.3 ), Check if kafka is being used
### kafka3.novalocal ( 10.0.0.3 ), Store node information in the /etc/hosts directory.
### kafka3.novalocal ( 10.0.0.3 ), Modify zookeeper.properties.
### kafka3.novalocal ( 10.0.0.3 ), Modify server.properties.
### kafka1.novalocal ( 10.0.0.1 ), Start Zookeeper, Kafka.
### Zookeeper, Kafka process is running.
### kafka2.novalocal ( 10.0.0.2 ), Start Zookeeper, Kafka.
### Zookeeper, Kafka process is running.
### kafka3.novalocal ( 10.0.0.3 ), Start Zookeeper, Kafka.
### Zookeeper, Kafka process is running.
##### Cluster Installation Complete #####
```


### Initial Setup After Creating Apache Kafka Instance
#### Change the Port
After initial installation, the ports are 9092, which is the Kafka default port, and 2181, which is the Zookeeper default port. It is recommended to change the port for security.

##### 1) Modify the ~/kafka/config/zookeeper.properties file
Open the ~/kafka/config/zookeeper.properties file and enter the Zookeeper port to change in clientPort.
```
shell> vi ~/kafka/config/zookeeper.properties

clientPort=zookeeper port to change
```
##### 2) Modify the ~/kafka/config/server.properties file
Open the ~/kafka/config/server.properties file and enter the Kafka port to change in listeners.

How to check Instance IP
```
Private IP on the console screen
or shell> hostname -i
```
```
shell> vi ~/kafka/config/server.properties

# Uncomment
listeners=PLAINTEXT://Instance IP:kafka port to change

# Change Zookeeper port
zookeeper.connect=Instance IP:zookeeper port to change
---> If it is a cluster, change Zookeeper port of each instance IP
```

##### 3) Restart Zookeeper, Kafka broker
Restart the zookeeper and the kafka for the port change to take effect.
```
shell> sudo systemctl stop kafka.service
shell> sudo systemctl stop zookeeper.service

shell> sudo systemctl start zookeeper.service
shell> sudo systemctl start kafka.service
```

##### 4) Check Zookeeper, Kafka Port Change
Check if the changed port is in use.
```
shell> netstat -ntl | grep [Kafka port]
shell> netstat -ntl | grep [Zookeeper port]
```
### Create and Use Apache Kafka Topic and Data

Create and query a topic
```
# Instance IP = Private IP / Kafka default port = 9092
# Create a topic
shell> ~/kafka/bin/kafka-topics.sh --create --bootstrap-server [Instance IP]:[Kafka PORT] --topic kafka

# Query a topic list
shell> ~/kafka/bin/kafka-topics.sh --list --bootstrap-server [Instance IP]:[Kafka PORT]

# Check the details of the topic
shell> ~/kafka/bin/kafka-topics.sh --describe --bootstrap-server [Instance IP]:[Kafka PORT] --topic kafka

# Delete a topic
shell> ~/kafka/bin/kafka-topics.sh --delete --bootstrap-server [Instance IP]:[Kafka PORT] --topic kafka
```
Create and use data
```
# Start producer
shell> ~/kafka/bin/kafka-console-producer.sh --broker-list  [Instance IP]:[Kafka PORT] --topic kafka

# Start consumer
shell> ~/kafka/bin/kafka-console-consumer.sh --bootstrap-server [Instance IP]:[Kafka PORT] --from-beginning --topic kafka
```

## Redis

### Start/Stop Redis
```
# Start Redis
shell> sudo systemctl start redis

# Stop Redis
shell> sudo systemctl stop redis

# Restart Redis
shell> sudo systemctl restart redis
```

### Connect to Redis
Connect to a Redis instance by using the `redis-cli` command.
```
shell> redis-cli
```

### Initial Setup After Creating a Redis Instance
The default configuration file for a Redis instance is the `~/redis/redis.conf` file. The description for the parameters to be changed is as follows.

#### Bind
- Default value: `127.0.0.1 -::1`
- Changed value: `<private ip> 127.0.0.1 -::1`

Value for an IP used by Redis. To allow access to a Redis instance from outside the server, add a private IP to the parameter. You can check the private IP with the `hostname -I` command.

#### Port
- Default value: `6379`

Port is 6379, a default value for Redis. It is recommended to change the port for security reasons. After changing the port, you can connect to Redis with the following command.

```
shell> redis-cli -p <new port>
```

#### Requirepass/masterauth
- Default value: `nhncloud`

The default password is `nhncloud`. For security reasons, it is recommended to change the password. If you are using replication connection, you must change the `requirepass` and `masterauth` values at the same time.

### Automatic HA Configuration Script
A Redis instance of NHN Cloud provides a script that automatically configures an HA environment. You can use the script only for **a new instance immediately after installation**, and cannot use after changing the set values from redis.conf.

To use the script, the following settings are required.

##### Copy key pair
The instance running the installation script must have a key pair (PEM file) required to connect to other instances. The key pair can be copied as follows.
- centos
```
local> scp -i <key pair>.pem <key pair>.pem centos@<floating ip>:/home/centos/
```
- ubuntu
```
local> scp -i <key pair>.pem <key pair>.pem ubuntu@<floating ip>:/home/ubuntu/
```

The key pairs for created instances must be the same.

##### Set security group
You must set a security group (**Network** > **Security Groups**) for communication between Redis instances. Create a security group with the following rules and apply it to a Redis instance.

| Direction | IP protocol | Port range| Ether| Remote|
| --- | --- | --- | --- | --- |
| Inbound |TCP | 6379| IPv4| Instance IP(CIDR)|
| Inbound |TCP | 16379| IPv4| Instance IP(CIDR)|
| Inbound |TCP | 26379| IPv4| Instance IP(CIDR)|

#### Sentinel Automatic Configuration
You will need 3 Redis instances to configure Sentinel. After copying the key pair to the instance used as the master, run the script as follows.

```
shell> sh .redis_make_sentinel.sh
```

Enter the private IPs of the master and replica in turn. You can check the private IP of each instance with the `hostname -I` command.

```
shell> sh .redis_make_sentinel.sh
Enter Master's IP: 192.168.0.33
Enter Replica-1's IP: 192.168.0.27
Enter Replica-2's IP: 192.168.0.97
```

Enter the file name of the copied key pair.
```
shell> Enter Pemkey's name: <key pair>.pem
```

#### Cluster Automatic Configuration
6 Redis instances are required for Cluster configuration. After copying the key pair to the instance used as the master, run the script as follows.

```
shell> sh .redis_make_cluster.sh
```

Enter the private IPs of Redis instances used for a cluster in turn. You can check the private IP of each instance with the `hostname -I` command.

```
shell> sh .redis_make_cluster.sh
Enter cluster-1'IP:  192.168.0.79
Enter cluster-2'IP: 192.168.0.10
Enter cluster-3'IP: 192.168.0.33
Enter cluster-4'IP:  192.168.0.116
Enter cluster-5'IP:  192.168.0.91
Enter cluster-6'IP:  192.168.0.32
```

Enter the file name of the copied key pair.

```
shell> Enter Pemkey's name: <key pair>.pem
```

Enter `yes` to complete cluster configuration.
```
>>> Performing hash slots allocation on 6 nodes...
Master[0] -> Slots 0 - 5460
Master[1] -> Slots 5461 - 10922
Master[2] -> Slots 10923 - 16383
Adding replica 192.168.0.91:6379 to 192.168.0.79:6379
Adding replica 192.168.0.32:6379 to 192.168.0.10:6379
Adding replica 192.168.0.116:6379 to 192.168.0.33:6379
M: 0a6ee5bf24141f0058c403d8cc42b349cdc09752 192.168.0.79:6379
   slots:[0-5460] (5461 slots) master
M: b5d078bd7b30ddef650d9a7fa9735e7648efc86f 192.168.0.10:6379
   slots:[5461-10922] (5462 slots) master
M: 0da9b78108b6581bdb90002cbdde3506e9173dd8 192.168.0.33:6379
   slots:[10923-16383] (5461 slots) master
S: 078b4ce014a52588e23577b3fc2dabf408723d68 192.168.0.116:6379
   replicates 0da9b78108b6581bdb90002cbdde3506e9173dd8
S: caaae4ebd3584c0481205e472d6bd0f9dc5c574e 192.168.0.91:6379
   replicates 0a6ee5bf24141f0058c403d8cc42b349cdc09752
S: ab2aa9e37cee48ef8e4237fd63e8301d81193818 192.168.0.32:6379
   replicates b5d078bd7b30ddef650d9a7fa9735e7648efc86f
Can I set the above configuration? (type 'yes' to accept):
```

```
[OK] All nodes agree about slots configuration.
>>> Check for open slots...
>>> Check slots coverage...
[OK] All 16384 slots covered.
```

## Apache Tomcat

### Default Location
The installation path of Tomcat is as follows.

```
~/apps/apache-tomcat-{version}/
```

### How to Start/Stop Tomcat

Tomcat is registered as a service by default during the initial installation process and is automatically started when the instance starts. You can use the command below to manually start or stop Tomcat.

``` sh
#Start the tomcat service
shell> sudo systemctl start tomcat

#Stop the tomcat service
shell> sudo systemctl stop tomcat

#Restart the tomcat service
shell> sudo systemctl restart tomcat
```

### Access the Tomcat Default Page
Tomcat runs on port 8080, which is the default from initial installation. You can access the Tomcat default page by running the following command:

```sh
shell> curl -i http://127.0.0.1:8080
HTTP/1.1 200
Content-Type: text/html;charset=UTF-8
...
```

### Initial Setup After Creating a Tomcat Instance

#### 1\. Change the Port
It runs with the default settings configured during initial installation. For security reasons, it is recommended to change the port.

##### 1) Modify the `server.xml` file

Open the `~/apps/apache-tomcat-{version}/conf/server.xml` file and enter the port address to change in \<Connector\>, as follows:

```sh
shell> vi ~/apps/apache-tomcat-{version}/conf/server.xml
```

```xml
...
<Connector port="{Port address to change}" protocol="HTTP/1.1"
            connectionTimeout="20000"
            redirectPort="8443" />
...
```

##### 2) Restart the service
Restart the Tomcat service for the port change to take effect.
```
shell> sudo systemctl restart tomcat
```

## Node.js

### Default Location
The installation path of Node.js is as follows.

```
~/apps/node-{version}/
```

### How to Run Node

```sh
# Write the app.js example code
shell> echo "console.log('Hello World')" > app.js

# Run node
shell> node app.js
Hello World
```

## Deep Learning Framework

### Create a Deep Learning Framework Image Template

To use the Deep Learning Framework, you must first create an image template.

From the service selection screen, go to **Compute > Image Builder > Create Image Template**.

Enter **Image Template Name**, select **linux - Ubuntu - Server xx.xx LTS** for OS, and set **Minimum Block Storage** to **70 GB** or more.

If you select the options, you will see **Deep Learning Framework** in the screen.

After selecting the script, click the **Confirm** button. In the pop-up that appears, click the **Create** button.

### Create a Deep Learning Framework Instance

After the image build is complete, click the **GPU Instance** button to actually create a GPU instance, and you will be taken to **Compute > GPU Instance > Create GPU Instance**.

When creating an instance, select the image built and created earlier to create an instance.

Deep Learning Framework Instance provides software as follows.

* TensorFlow, PyTorch
* NVIDIA CUDA Toolkit, cuDNN, NCCL, TensorRT
* Intel MKL

NVIDIA cuDNN contains source code provided by NVIDIA Corporation. [License](https://docs.nvidia.com/deeplearning/cudnn/latest/reference/eula.html)

After completing the setup, create an instance. For more information on instance creation, see [Instance Overview](http://docs.toast.com/en/Compute/Instance/en/overview/).

### Check Installed Development Environment

Use the conda command to check the development environment installed by Miniconda.

```
$ conda info --envs
# conda environments:
#
                         /opt/intel/oneapi/intelpython/latest
                         /opt/intel/oneapi/intelpython/latest/envs/2021.4.0
base                  *  /root/miniconda3
pt_py38                  /root/miniconda3/envs/pt_py38
tf2_py38                 /root/miniconda3/envs/tf2_py38
```

>[Note]
>
>For more detailed instructions, refer to [Miniconda documentation](https://docs.conda.io/en/latest/miniconda.html).

### How to Use TensorFlow

First, activate the TensorFlow environment.

```
(base) root@b64e6a035884:~# conda activate tf2_py38
(tf2_py38) root@b64e6a035884:~#
```

Test TensorFlow training as follows:

```
$ cd ~/
$ git clone https://github.com/tensorflow/models.git
$ cd models
$ git checkout tags/v2.4.0
$ git status
HEAD detached at v2.4.0
nothing to commit, working tree clean

$ mkdir $HOME/models/model
$ mkdir $HOME/models/dataset
$ vim train.sh
#!/bin/bash


export PYTHONPATH=$HOME/models
export NCCL_DEBUG=INFO
MODEL_DIR=$HOME/models/model
DATA_DIR=$HOME/models/dataset
# Set when one or more GPU is used
NUM_GPUS=1 # Example: NUM_GPUS=2

python $HOME/models/official/vision/image_classification/mnist_main.py \
  --model_dir=$MODEL_DIR \
  --data_dir=$DATA_DIR \
  --train_epochs=2 \
  --distribution_strategy=mirrored \ # Set when one or more GPU is used
  --num_gpus=$NUM_GPUS \ # Set when one or more GPU is used
  --download

$ chmod +x train.sh
$ ./train.sh
```

>[Note]
>
>For more detailed instructions, refer to [TensorFlow Tutorial](https://www.tensorflow.org/tutorials).

### How to Use PyTorch

First, activate the PyTorch environment.

```
(tf2_py38) root@b64e6a035884:~# conda deactivate
(base) root@b64e6a035884:~# conda activate pt_py38
(pt_py38) root@b64e6a035884:~#
```

Test PyTorch training as follows:

```
$ cd ~/
$ git clone https://github.com/pytorch/examples.git
$ cd examples/mnist
$ python main.py --epochs 1
```

>[Note]
>
>For more detailed instructions, refer to [PyTorch Tutorial](https://pytorch.org/tutorials/).


## Slurm

This component installs and sets up the MUNGE package, and installs the Slurm package. For details on how to run Slurm, see [Slurm Installation Guide](https://slurm.schedmd.com/quickstart_admin.html).

>[Note]
>
>All commands included in this section must be executed with the root privilege.

### Preparation

#### 1. Modify the `hosts` file

Open the `/etc/hosts` file and enter the IP and alias of the node to be configured in the cluster environment.

``` console
# vi /etc/hosts
```

#### 2. Modify the `hostname` file

Open the `/etc/hostname` file and match the alias of the current node with the alias in the `hosts` file.

``` console
# vi /etc/hostname
```


### Cluster Configuration and Slurm Settings

There is no default configuration applied initially, so you must write a configuration file. Write the configuration by referring to [Slurm Configuration Guide](https://slurm.schedmd.com/quickstart_admin.html#Config) and [Slurm Configuration Tool](https://slurm.schedmd.com/configurator.html), and save it in `/etc/slurm/slurm.conf` file.

The log file path must be specified as a directory under the `/var/log/slurm/` path. If you want to specify a different path, you must specify the path in the configuration file and match the owner of the directory with the `SlurmUser` setting value.


### Running Slurm

You must configure all of the clusters and set the configuration before running Slurm. For more information, see [Slurm Installation Guide](https://slurm.schedmd.com/quickstart_admin.html) and [Slurm Quick Start Guide](https://slurm.schedmd.com/quickstart.html).


## NHN Kubernetes Service(NKS) Worker Node

You can create an image that can be used as a worker node for NHN Kubernetes Service (NKS). For more information, see [NKS User Guide](https://docs.nhncloud.com/en/Container/NKS/en/user-guide/#_25).
