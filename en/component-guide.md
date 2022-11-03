## Compute > Image Builder > Installation Component Guide

## PostgreSQL 

> [Note]
> This guide is based on PostgreSQL version 13.
> If you are using a different version, please change the commands accordingly.

### How to Start/Stop PostgreSQL

```
#Start the postgresql service
shell> systemctl start postgresql-13

#Stop the postgresql service
shell> systemctl stop postgresql-13

#Restart the postgresql service
shell> systemctl restart postgresql-13
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
shell> vi /var/lib/pgsql/13/data/postgresql.conf


#Enter the port to use in the postgresql.conf file.

port =port name to use


#Save vi editor


#Restart the postgresql service

shell> systemctl restart postgresql-13


#Connect to the changed port as follows

shell> psql -p [changed port number]
```

#### 2\. Change the Server Log Time Zone

The default time zone logged in the server log is set to UTC. It is recommended to change it to be the same as the SYSTEM local time.
<br>
```
shell> vi /var/lib/pgsql/13/data/postgresql.conf


#Enter the time zone to use in the postgresql.conf file.

log_timezone =time zone to use


#Save vi editor


#Restart the postgresql service

shell> systemctl restart postgresql-13


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
shell> vi /var/lib/pgsql/13/data/postgresql.conf


#Specify the addresses to allow in the postgresql.conf file.
#0.0.0.0 to allow all IPv4 addresses
#:: to allow all IPv6 addresses
#* to allow any address

listen_addresses =Addresses to allow


#Save vi editor


shell> vi /var/lib/pgsql/13/data/pg_hba.conf


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

shell> systemctl restart postgresql-13
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

#### 1\. Set the Password

After initial installation, the MySQL root account password is not set. Therefore, you must set a password after installation.

```
SET PASSWORD [FOR user] = password_option

mysql> SET PASSWORD = PASSWORD('password');
```

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
shell> mysql -u root
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

## KAFKA
> [Note]
> This guide is created based on Kafka version 3.3.1.
> If you are using a different version, please makes changes accordingly.

### Start and Stop Zookeeper, Kafka
```
# Start Zookeeper, Kafka (Zookeeper first)
shell> sudo systemctl start zookeeper.service
shell> sudo systemctl start kafka.service

# Stop Zookeeper, Kafka (Kafka first)
shell> sudo systemctl stop kafka.service
shell> sudo systemctl stop zookeeper.service

# Restart Zookeeper, Kafka
shell> sudo systemctl restart zookeeper.service
shell> sudo systemctl restart kafka.service
```

### Install Kafka Cluster
- Must install in a new instance.
- An odd number of instances (3 or more) are required, and the installation script is executed in the instance.
- An instance consists of of one kafka broker and one zookeeper node.
- The key pair (PEM file) required to connect to another instance must be located at the /home/centos/ path of the instance running the installation script. The key pair of cluster instances must be the same.
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
shell> sh /home/centos/.make_cluster.sh

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


### Initial Setup After Creating a Kafka Instance
#### Change the Port
After initial installation, the ports are 9092, which is the Kafka default port, and 2181, which is the Zookeeper default port. It is recommended to change the port for security.

##### 1) Modify the /home/centos/kafka/config/zookeeper.properties file
Open the /home/centos/kafka/config/zookeeper.properties file and enter the Zookeeper port to change in clientPort.
```
shell> vi /home/centos/kafka/config/zookeeper.properties

clientPort=[zookeeper port to change]
```
##### 2) Modify the /home/centos/kafka/config/server.properties file
Open the /home/centos/kafka/config/server.properties file and enter the Kafka port to change in listeners.
```
shell> vi /home/centos/kafka/config/server.properties

# Uncomment
listeners=PLAINTEXT://[Private IP]:[kafka port to change]

# Change Zookeeper port
zookeeper.connect=Instance IP:[Zookeeper port]
---> If it is a cluster, change the port of each instance IP
```

##### 3) Restart Zookeeper, Kafka
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

### Create and Use Kafka Topic. Data 

Create and query a topic
```
# Instance IP = Private IP / Kafka default port = 9092
# Create a topic
shell> /home/centos/kafka/bin/kafka-topics.sh --create --bootstrap-server [Instance IP]:[Kafka PORT] --topic kafka

# Query a topic list
shell> /home/centos/kafka/bin/kafka-topics.sh --list --bootstrap-server [Instance IP]:[Kafka PORT]

# Check the details of the topic
shell> /home/centos/kafka/bin/kafka-topics.sh --describe --bootstrap-server [Instance IP]:[Kafka PORT] --topic kafka

# Delete a topic
shell> /home/centos/kafka/bin/kafka-topics.sh --delete --bootstrap-server [Instance IP]:[Kafka PORT] --topic kafka
```
Create and use data
```
# Start producer
shell> /home/centos/kafka/bin/kafka-console-producer.sh --broker-list  [Instance IP]:[Kafka PORT] --topic kafka

# Start consumer
shell> /home/centos/kafka/bin/kafka-console-consumer.sh --bootstrap-server [Instance IP]:[Kafka PORT] --from-beginning --topic kafka
```

## JEUS, WebtoB

> [Note]
> This guide is based on JEUS 8 Fxi#1 and WebtoB 5 Fix4.
> If you are using a different version, please change the commands accordingly.

Each image script installs DAS, MS, and WebtoB after installing JDK.
For how to set up and control the services after installation, refer to TmaxSoft's guide document ([JEUS](https://technet.tmaxsoft.com/upload/download/online/jeus/pver-20190227-000001/index.html), [WebtoB](https://technet.tmaxsoft.com/upload/download/online/webtob/pver-20201021-000001/index.html)).

### Install Images

The JDK will be installed in `~/apps/jdk8u292`, and a link will be created from that directory to `~/apps/jdk8`.
During the JDK installation process, the path `~/apps/jdk8/bin` is added to `PATH` in `.bash_profile`.
If you already have a `~/apps/jdk8` directory, the JDK will not be installed.

#### JEUS DAS, MS

JEUS is installed in `~/apps/jeus8`.


The following properties are set during installation.

| Property | Default value | 
| --- | --- |
| Domain name | jeus_domain |
| WebAdmin port | 9736 |
| Admin server name | adminServer |
| Admin user ID | administrator |
| Admin user password | jeusadmin |
| Node manager | java |

#### WebtoB

WebtoB is installed in `~/apps/webtob`.

### Check JEUS, WebtoB Startup

#### JEUS

To configure or control JEUS, start the node manager and then control it through WebAdmin or jeusadmin.

##### Start the Node Manager

Connect to the shell and run the node manager with the startNodeManager command.
Since the node managers need to communicate with each other, add an allow rule for the default port 7730 to the security group.

##### Start JEUS

Run DAS with the startDomainAdminServer command.
```
startDomainAdminServer -uadministrator -pjeusadmin
```

##### JEUS WebAdmin

Run WebAdmin as follows:

1. Set a floating IP on the instance where DAS is installed.
2. Add an allow rule for port 9736 to that instance's security group.
3. If you connect to `http://{floating IP}:9736/webadmin` in a web browser, you can see the WebAdmin screen.


#### WebtoB

Compile the configuration file using the wscfl command.
```
wscfl -i http.m
```

Start WebtoB using wsboot.
```
wsboot 
```

You can use wsadmin to view or control the status.

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

Enter **Image Template Name**, select **linux - Ubuntu - Server 18.04 LTS** for OS, and set **Minimum Block Storage** to **70 GB** or more.

If you select the options, you will see **Deep Learning Framework** in the screen.

After selecting the script, click the **Confirm** button. In the pop-up that appears, click the **Create** button.

### Create a Deep Learning Framework Instance

After the image build is complete, click the **GPU Instance** button to actually create a GPU instance, and you will be taken to **Compute > GPU Instance > Create GPU Instance**.

When creating an instance, select the image built and created earlier to create an instance.

Deep Learning Framework Instance provides the following versions of software:

| Software | Version | Installation method |
| --- | --- | --- | 
| TensorFlow | 2.4.1 | pip, [Reference](https://www.tensorflow.org/install/pip) |
| PyTorch | 1.7.1 | conda, [Reference](https://pytorch.org/get-started/previous-versions/) |
| Python | 3.8.11 | conda |
| OS | Ubuntu 18.04 LTS | N/A |
| NVIDIA Driver | 450.102.04 | apt |
| NVIDIA CUDA | 11.0 | apt |
| NVIDIA cuDNN | 8.0.4 | apt |
| NVIDIA NCCL | 2.7.8 | apt |
| NVIDIA TensorRT | 7.1.3 | apt |
| Intel oneAPI MKL | 2021.4.0 | apt |

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
