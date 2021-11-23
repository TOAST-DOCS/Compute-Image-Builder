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

After creating an instance, initially connect to PostgreSQL as follows.
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

After creating an instance, initially connect to MariaDB as follows.

``` sh
shell> mysql -u root
```

After changing the password, connect to MariaDB as follows.

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

After creating an instance, initially connect to MariaDB as follows.

``` sh
shell> mysql -u root
```

After changing the password, connect to MariaDB as follows.

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

After creating an instance, initially connect to CUBRID as follows.

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
