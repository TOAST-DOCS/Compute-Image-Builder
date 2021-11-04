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

> [참고]
> 본 가이드는 JEUS 8 Fxi#1, WebtoB 5 Fix4  버전을 기준으로 작성되었습니다.
> 다른 버전을 사용하시는 경우 해당 버전에 맞게 변경해 주십시오.

각 이미지 스크립트는 JDK 설치 후 DAS, MS, WebtoB를 설치합니다.
설치 이후의 설정이나 제어 방법은 TmaxSoft의 가이드 문서([Jeus](https://technet.tmaxsoft.com/upload/download/online/jeus/pver-20190227-000001/index.html), [WebtoB](https://technet.tmaxsoft.com/upload/download/online/webtob/pver-20201021-000001/index.html))를 참고하시기 바랍니다.

### 이미지 설치

JDK는 `/home1/centos/apps/jdk8u292`에 설치되며, 해당 디렉토리에서 `/home1/centos/apps/jdk8`로 링크가 생성됩니다.
JDK 설치 과정에서 `.bash_profile`의 `PATH`에 `/home1/centos/apps/jdk8/bin` 경로가 추가됩니다.
이미 `/home1/centos/apps/jdk8` 디렉토리가 있다면 JDK가 설치되지 않습니다.

#### JEUS DAS, MS

Jeus는 `/home1/centos/apps/jeus8`에 설치됩니다. (스크립트 등에서 내부적으로 정해진 디렉토리에 설치하는 경우)


설치 시 아래 속성들로 설정됩니다.

| 구분 | 기본값 | 
| --- | --- |
| 도메인 이름 | jeus_domain |
| WebAdmin 포트 | 9736 |
| 어드민 서버 이름 | adminServer |
| 어드민 유저 아이디 | administrator |
| 어드민 유저 비밀번호 | jeusadmin |
| 노드 매니저 | java |

#### WebtoB

WetoB는 `/home1/centos/apps/webtob` 에 설치를 합니다.

### JEUS, WebtoB 기동 확인

#### JEUS

JEUS를 설정하거나 제어하려면 노드 매니저를 기동한 후 WebAdmin이나 jeusadmin을 통해서 제어해야 합니다.

##### 노드 매니저 기동

셸로 접속하여 startNodeManager 명령어로 노드 매니저를 실행합니다.
노드 매니저끼리 통신이 필요하므로 보안 그룹에 기본 포트인 7730 에 대한 허용 규칙을 추가해야 합니다.

##### JEUS 기동

DAS 는 startDomainAdminServer 명령어로 실행을 합니다.
```
startDomainAdminServer -uadministrator -pjeusadmin
```

##### JEUS WebAdmin

DAS가 설치된 인스턴스에 플롯팅 IP를 설정 후
웹브라우저에서 `http://플롯팅IP:9736/webadmin` 으로 접속하면
WebAdmin 화면을 볼 수 있습니다.
(9736 포트에 대해서 보안 그룹에 허용 추가를 해야 합니다.)


#### WebtoB

wscfl 명령어를 이용하여 설정 파일을 컴파일 합니다.
```
wscfl -i http.m
```

wsboot를 이용하여 기동을 합니다.
```
wsboot 
```

wsadmin 을 이용하여 상태를 확인하거나 제어를 할수 있습니다.

