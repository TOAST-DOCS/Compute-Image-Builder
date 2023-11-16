## Compute > Image Builder > 설치 구성 요소 가이드

## PostgreSQL

> [참고]
> 본 가이드는 PostgreSQL 13 버전을 기준으로 작성되었습니다.
> 다른 버전을 사용하시는 경우 해당 버전에 맞게 변경해 주십시오.

### PostgreSQL 시작/정지 방법

```
#postgresql 서비스 시작
shell> systemctl start postgresql-13

#postgresql 서비스 중지
shell> systemctl stop postgresql-13

#postgresql 서비스 재시작
shell> systemctl restart postgresql-13
```

### PostgreSQL 접속

인스턴스를 생성한 후 초기에는 아래와 같이 접속합니다.
<br>
```
#postgres로 계정 전환 후 접속
shell> su - postgres
shell> psql
```

### PostgreSQL 인스턴스 생성 후 초기 설정

#### 1\. 포트\(port\) 변경

제공되는 이미지 포트는 PostgreSQL 기본 포트인 5432입니다. 보안상 포트 변경을 권장합니다.
<br>
```
shell> vi /var/lib/pgsql/13/data/postgresql.conf


#postgresql.conf 파일에 사용할 포트를 입력합니다.

port =사용할 포트명


#vi 편집기 저장


#postgresql 서비스 재시작

shell> systemctl restart postgresql-13


#변경된 포트로 아래와 같이 접속

shell> psql -p[변경된 포트 번호]
```

#### 2\. 서버 로그 타임 존 변경

서버 로그에 기록되는 기본 시간대가 UTC로 설정되어 있습니다. SYSTEM 로컬 시간과 동일하게 변경할 것을 권장합니다.
<br>
```
shell> vi /var/lib/pgsql/13/data/postgresql.conf


#postgresql.conf 파일에 사용할 타임 존을 입력합니다.

log_timezone =사용할 타임 존


#vi 편집기 저장


#postgresql 서비스 재시작

shell> systemctl restart postgresql-13


#postgresql 접속

shell> psql


#변경한 설정 확인

postgres=# SHOW log_timezone;
```

#### 3\. public 스키마 권한 취소

기본적으로 모든 사용자에게 public 스키마에 대한 CREATE 및 USAGE 권한을 부여하고 있으므로 데이터베이스에 접속할 수 있는 사용자는 public 스키마에서 객체를 생성할 수 있습니다. 모든 사용자가 public 스키마에서 객체를 생성하지 못하도록 권한 취소를 권장합니다.
<br>
```
#postgresql 접속

shell> psql


#권한 취소 명령어 실행

postgres=# REVOKE CREATE ON SCHEMA public FROM PUBLIC;
```

#### 4\. 원격 접속 허용

로컬 호스트 이외의 접속을 허용하려면 listen_addresses 변수와 클라이언트 인증 설정 파일을 변경해야 합니다.
<br>
```
shell> vi /var/lib/pgsql/13/data/postgresql.conf


#postgresql.conf 파일에 허용할 주소를 명시해 줍니다.
#IPv4 주소를 모두 허용하는 경우 0.0.0.0
#IPv6 주소를 모두 허용하는 경우 ::
#모든 주소를 허용하는 경우 *

listen_addresses =허용할 주소


#vi 편집기 저장


shell> vi /var/lib/pgsql/13/data/pg_hba.conf


#IP 주소 형식별로 클라이언트 인증 제어
#오래된 클라이언트 라이브러리는 scram-sha-256 방식이 지원되지 않으므로 md5로 변경 필요

# TYPE  DATABASE        USER            ADDRESS                 METHOD
# IPv4 local connections:
host    all             all             127.0.0.1/32            scram-sha-256
host    허용 DB          허용 유저         허용 주소                  scram-sha-256
# IPv6 local connections:
host    all             all             ::1/128                 scram-sha-256
host    허용 DB          허용 유저         허용 주소                  scram-sha-256


#postgresql 서비스 재시작

shell> systemctl restart postgresql-13
```

### PostgreSQL 디렉터리 설명

PostgreSQL 디렉터리 및 파일 설명은 아래와 같습니다.

| 이름 | 설명 |
| --- | --- |
| postgresql.cnf | /var/lib/pgsql/{version}/data/postgresql.cnf |
| initdb.log | PostgreSQL 데이터베이스 클러스터 생성 log - /var/lib/pgsql/{version}/initdb.log |
| DATADIR | PostgreSQL 데이터 파일 경로 - /var/lib/pgsql/{version}/data/ |
| LOG | PostgreSQL log 파일 경로 - /var/lib/pgsql/{version}/data/log/\*.log |

## MySQL

### MySQL 시작/정지 방법

- centos
``` sh
# MySQL 서비스 시작
shell> sudo systemctl start mysqld

# MySQL 서비스 종료
shell> sudo systemctl stop mysqld

# MySQL 서비스 재시작
shell> sudo systemctl restart mysqld
```

- ubuntu
``` sh
# MySQL 서비스 시작
shell> sudo systemctl start mysql

# MySQL 서비스 종료
shell> sudo systemctl stop mysql

# MySQL 서비스 재시작
shell> sudo systemctl restart mysql
```

### MySQL 접속

이미지 생성 후 초기에는 아래와 같이 접속합니다.

``` sh
shell> mysql -u root
```

패스워드 변경 후에는 아래와 같이 접속합니다.

``` sh
shell> mysql -u root -p
Enter password:
```

### MySQL 인스턴스 생성 후 초기 설정

#### 1\. 비밀번호 설정

초기 설치 후 MySQL ROOT 계정 비밀번호는 지정되어 있지 않습니다. 그러므로 설치 후 반드시 바로 비밀번호를 설정해야 합니다. 비밀번호는 아래와 같이 변경할 수 있습니다.
```
mysql> ALTER USER USER() IDENTIFIED BY '새로운 비밀번호';
```
MySQL의 기본 validate_password_policy는 아래와 같습니다.
- validate_password_policy=MEDIUM
- 기본 8자 이상, 숫자, 소문자, 대문자, 특수문자를 포함해야 함

#### 2\. 포트\(port\) 변경

초기 설치 후 포트는 MySQL의 기본 포트인 3306입니다. 보안상 포트 변경을 권장합니다.

##### 1) `/etc/my.cnf` 파일 수정

`/etc/my.cnf` 파일에 사용하고자 하는 포트를 명시해 줍니다.

```
shell> sudo vi /etc/my.cnf
```

```
port=[변경할 port 주소]
```

##### 2) 인스턴스 재시작
포트 변경이 적용되도록 인스턴스를 재시작합니다.
```
sudo systemctl restart mysqld
```

## MariaDB

### MariaDB 시작/정지 방법

``` sh
# MariaDB 서비스 시작
shell> sudo systemctl start mariadb.service

# MariaDB 서비스 종료
shell> sudo systemctl stop mariadb.service

# MariaDB 서비스 재시작
shell> sudo systemctl restart mariadb.service
```

### MariaDB 접속

이미지 생성 후 초기에는 아래와 같이 접속합니다.

``` sh
shell> sudo mysql -u root
```

패스워드 변경 후에는 아래와 같이 접속합니다.

``` sh
shell> mysql -u root -p
Enter password:
```

### MariaDB 인스턴스 생성 후 초기 설정

#### 1\. 비밀번호 설정

초기 설치 후 MariaDB root 계정 비밀번호는 지정되어 있지 않습니다. 그러므로 설치 후 반드시 비밀번호를 설정해야 합니다.

```
SET PASSWORD [FOR user] = password_option

MariaDB> SET PASSWORD = PASSWORD('비밀번호');
```

#### 2\. 포트\(port\) 변경

초기 설치 후 포트는 MariaDB의 기본 포트인 3306입니다. 보안상 포트 변경을 권장합니다.

##### 1) `/etc/my.cnf.d/servfer.cnf` 파일 수정

`/etc/my.cnf.d/server.cnf` 파일을 열어서 [mariadb] 밑에 아래와 같이 변경할 포트 주소를 입력합니다.

```
shell> sudo vi /etc/my.cnf.d/server.cnf
```

```
[mariadb]
port=[변경할 port 주소]
```

##### 2) 인스턴스 재시작
포트 변경이 적용되도록 인스턴스를 재시작합니다.
```
sudo systemctl restart mariadb.service
```

## CUBRID

### CUBRID 서비스 시작/정지 방법

“cubrid” Linux 계정으로 로그인하여 CUBRID 서비스를 다음과 같이 시작하거나 종료할 수 있습니다.

``` sh
# CUBRID 서비스/서버 시작
shell> sudo su - cubrid
shell> cubrid service start
shell> cubrid server start demodb

# CUBRID 서비스/서버 종료
shell> sudo su - cubrid
shell> cubrid server stop demodb
shell> cubrid service stop

# CUBRID 서비스/서버 재시작
shell> sudo su - cubrid
shell> cubrid server restart demodb
shell> cubrid service restart

# CUBRID 브로커 시작/종료/재시작
shell> sudo su - cubrid
shell> cubrid broker start
shell> cubrid broker stop
shell> cubrid broker restart
```

### CUBRID 접속

이미지 생성 후 아래와 같이 접속합니다.

``` sh
shell> sudo su - cubrid
shell> csql -u dba demodb@localhost
```

### CUBRID 인스턴스 생성 후 초기 설정

#### 1\. 비밀번호 설정

초기 설치 후 CUBRID dba 계정 비밀번호는 지정되어 있지 않습니다. 그러므로 설치 후 반드시 비밀번호를 설정해야 합니다.

```
shell> csql -u dba -c "ALTER USER dba PASSWORD 'new_password'" demodb@localhost
```

#### 2\. broker 포트\(port\) 변경

**query\_editor**의 브로커 포트는 기본값이 **30000**으로 설정되며, **broker1**의 브로커 포트는 기본값이 **33000**으로 설정됩니다.
보안상 포트 변경을 권장합니다.

##### 1) broker 파일 수정

`/opt/cubrid/conf/cubrid\_broker.conf` 파일을 열어서 아래와 같이 변경할 포트 주소를 입력합니다.

```
shell> vi /opt/cubrid/conf/cubrid_broker.conf
```

```
[%query_editor]
BROKER_PORT             =[변경할 port 주소]

[%BROKER1]
BROKER_PORT             =[변경할 port 주소]
```

##### 2) broker 재시작
포트 변경이 적용되도록 broker를 재시작합니다.
```
shell> cubrid broker restart
```

## Apache Kafka
> [참고]
> 본 가이드는 Kafka 3.3.1 버전을 기준으로 작성되었습니다.
> 다른 버전을 사용하시는 경우 해당 버전에 맞게 변경해 주십시오.
> 인스턴스 타입은 c1m2(CPU 1core, Memory 2GB) 이상 사양으로 생성해 주십시오.

### Zookeeper, Kafka broker 시작/정지
```
# Zookeeper, Kafka broker 시작(Zookeeper 먼저 시작)
shell> sudo systemctl start zookeeper.service
shell> sudo systemctl start kafka.service

# Zookeeper, Kafka broker 종료(Kafka broker 먼저 종료)
shell> sudo systemctl stop kafka.service
shell> sudo systemctl stop zookeeper.service

# Zookeeper, Kafka broker 재시작
shell> sudo systemctl restart zookeeper.service
shell> sudo systemctl restart kafka.service
```

### Kafka Cluster 설치
- 반드시 신규 인스턴스에 설치합니다.
- 인스턴스는 3대 이상 홀수로 필요하며, 인스턴스 1대에서 설치 스크립트를 수행합니다.
- 인스턴스 1대에 kafka broker, zookeeper node 각 1개씩 같이 구성됩니다.
- 설치 스크립트를 수행하는 인스턴스의 ~ 경로에 타 인스턴스 접속 시 필요한 키 페어(PEM 파일)가 있어야 합니다. 클러스터 인스턴스들의 키 페어는 모두 동일해야 합니다.
- 기본 포트 설치만 지원합니다. 포트 변경이 필요할 경우 클러스터 설치를 완료한 뒤 초기 설정 가이드의 포트 변경을 참고하여 변경합니다.
- 인스턴스 간 Kafka 관련 포트 통신을 위해 아래 보안 그룹 설정을 추가합니다.

보안 그룹 설정
```
방향: 수신
IP 프로토콜: TCP
포트: 22, 9092, 2181, 2888, 3888
```
Hostname, IP 확인 방법
```
# Hostname 확인
shell> hostname

# IP 확인
콘솔 화면
또는 shell> hostname -i
```
Cluster 설치 스크립트 수행 예시(위에서 확인한 hostname, IP 입력)
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

### Apache Kafka 인스턴스 생성 후 초기 설정
#### 포트(port) 변경
최초 설치 후 포트는 Kafka 기본 포트인 9092, Zookeeper 기본 포트인 2181입니다. 보안을 위해 포트를 변경할 것을 권장합니다.

##### 1) ~/kafka/config/zookeeper.properties 파일 수정
~/kafka/config/zookeeper.properties 파일을 열어서 clientPort에 변경할 Zookeeper port를 입력합니다.
```
shell> vi ~/kafka/config/zookeeper.properties

clientPort=변경할 zookeeper port
```
##### 2) ~/kafka/config/server.properties 파일 수정
~/kafka/config/server.properties 파일을 열어서 listeners에 변경할 Kafka port를 입력합니다.

인스턴스 IP 확인 방법
```
콘솔 화면의 Private IP
또는 shell> hostname -i
```
```
shell> vi ~/kafka/config/server.properties

# 주석 해제
listeners=PLAINTEXT://인스턴스 IP:변경할 kafka port

# Zookeeper 포트 변경
zookeeper.connect=인스턴스 IP:변경할 zookeeper port
---> 클러스터인 경우, 각 인스턴스 IP의 Zookeeper port 변경
```

##### 3) Zookeeper, Kafka broker 재시작
```
shell> sudo systemctl stop kafka.service
shell> sudo systemctl stop zookeeper.service

shell> sudo systemctl start zookeeper.service
shell> sudo systemctl start kafka.service
```

##### 4) Zookeeper, Kafka port 변경 확인
변경된 포트가 사용되고 있는지 확인합니다.
```
shell> netstat -ntl | grep [Kafka port]
shell> netstat -ntl | grep [Zookeeper port]
```

### Apache Kafka 토픽 및 데이터 생성/사용

토픽 생성/조회
```
# 인스턴스IP = Private IP / Kafka 기본 port = 9092
# 토픽 생성
shell> ~/kafka/bin/kafka-topics.sh --create --bootstrap-server [인스턴스IP]:[카프카PORT] --topic kafka

# 토픽 리스트 조회
shell> ~/kafka/bin/kafka-topics.sh --list --bootstrap-server [인스턴스IP]:[카프카PORT]

# 토픽 상세 정보 확인
shell> ~/kafka/bin/kafka-topics.sh --describe --bootstrap-server [인스턴스IP]:[카프카PORT] --topic kafka

# 토픽 삭제
shell> ~/kafka/bin/kafka-topics.sh --delete --bootstrap-server [인스턴스IP]:[카프카PORT] --topic kafka
```
데이터 생성/사용
```
# producer 시작
shell> ~/kafka/bin/kafka-console-producer.sh --broker-list [인스턴스IP]:[카프카PORT] --topic kafka

# consumer 시작
shell> ~/kafka/bin/kafka-console-consumer.sh --bootstrap-server [인스턴스IP]:[카프카PORT] --from-beginning --topic kafka
```

## Redis

### Redis 시작/정지
```
# Redis 서비스 시작
shell> sudo systemctl start redis

# Redis 서비스 중지
shell> sudo systemctl stop redis

# Redis 서비스 재시작
shell> sudo systemctl restart redis
```

### Redis 접속
`redis-cli` 커맨드를 이용해 Redis 인스턴스에 접속할 수 있습니다.
```
shell> redis-cli
```

### Redis 인스턴스 생성 후 초기 설정
Redis 인스턴스의 기본 설정 파일은 `~/redis/redis.conf` 입니다. 변경해야 할 파라미터에 대한 설명은 아래와 같습니다.

#### bind
- 기본 값: `127.0.0.1 -::1`
- 변경 값: `<private ip> 127.0.0.1 -::1`

Redis가 사용할 ip에 대한 값입니다. 서버 외부에서 Redis 인스턴스로의 접근을 허용하려면 해당 파라미터에 private ip를 추가해야 합니다. private ip는 `hostname -I` 커맨드로 확인할 수 있습니다.

#### port
- 기본 값: `6379`

포트는 Redis 기본값인 6379입니다. 보안상 포트 변경을 권장합니다. 포트를 변경한 뒤에는 아래 커맨드로 Redis에 접속할 수 있습니다.

```
shell> redis-cli -p <새로운 포트>
```

#### requirepass/masterauth
- 기본 값: `nhncloud`

기본 비밀번호는 `nhncloud`입니다. 보안상 비밀번호 변경을 권장합니다. 복제 연결을 사용할 경우 `requirepass`와 `masterauth`값을 동시에 변경해야 합니다.

### 자동 HA 구성 스크립트
NHN Cloud의 Redis 인스턴스는 자동으로 HA 환경을 구성해 주는 스크립트를 제공합니다. 스크립트는 반드시 **설치 직후의 신규 인스턴스**에서만 사용할 수 있으며, redis.conf에서 설정값을 변경한 경우에는 사용할 수 없습니다.

스크립트를 사용하기 위해서는 다음 설정이 필수적으로 필요합니다.

##### 키 페어 복사
설치 스크립트를 수행하는 인스턴스에 타 인스턴스 접속에 필요한 키 페어(PEM 파일)가 있어야 합니다. 키 페어는 다음과 같이 복사할 수 있습니다.

- centos
```
local> scp -i <키 페어>.pem <키 페어>.pem centos@<floating ip>:/home/centos/
```
- ubuntu
```
local> scp -i <키 페어>.pem <키 페어>.pem ubuntu@<floating ip>:/home/ubuntu/
```

생성한 인스턴스들의 키 페어는 모두 동일해야 합니다.

##### 보안 그룹 설정
Redis 인스턴스간의 통신을 위해 보안 그룹(**Network** > **Security Groups**) 설정이 필요합니다. 아래 규칙으로 보안 그룹을 생성한 뒤 Redis 인스턴스에 적용하세요.

| 방향 | IP 프로토콜| 포트 범위| Ether| 원격|
| --- | --- | --- | --- | --- |
| 수신|TCP | 6379| IPv4| 인스턴스 IP(CIDR)|
| 수신|TCP | 16379| IPv4| 인스턴스 IP(CIDR)|
| 수신|TCP | 26379| IPv4| 인스턴스 IP(CIDR)|

#### Sentinel 자동구성
Sentinel 구성을 위해 3개의 Redis 인스턴스가 필요합니다. 마스터로 사용할 인스턴스에 키 페어를 복사한 뒤 아래와 같이 스크립트를 수행하세요.

```
shell> sh .redis_make_sentinel.sh
```

이후 마스터와 복제본의 private IP를 차례로 입력합니다. 각 인스턴스의 private IP는 `hostname -I` 커맨드로 확인할 수 있습니다.

```
shell> sh .redis_make_sentinel.sh
Enter Master's IP: 192.168.0.33
Enter Replica-1's IP: 192.168.0.27
Enter Replica-2's IP: 192.168.0.97
```

복사해 온 키 페어의 파일명을 입력합니다.
```
shell> Enter Pemkey's name: <키 페어>.pem
```

#### Cluster 자동 구성
Cluster 구성을 위해 6개의 Redis 인스턴스가 필요합니다. 마스터로 사용할 인스턴스에 키 페어를 복사한 뒤 아래와 같이 스크립트를 수행하세요.

```
shell> sh .redis_make_cluster.sh
```

이후 클러스터에 사용할 Redis 인스턴스의 private IP를 차례로 입력합니다. 각 인스턴스의 private IP는 `hostname -I` 커맨드로 확인할 수 있습니다.

```
shell> sh .redis_make_cluster.sh
Enter cluster-1'IP:  192.168.0.79
Enter cluster-2'IP: 192.168.0.10
Enter cluster-3'IP: 192.168.0.33
Enter cluster-4'IP:  192.168.0.116
Enter cluster-5'IP:  192.168.0.91
Enter cluster-6'IP:  192.168.0.32
```

복사해 온 키 페어의 파일명을 입력합니다.

```
shell> Enter Pemkey's name: <키 페어>.pem
```

`yes`를 입력해 클러스터 구성을 완료합니다.
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

## JEUS, WebtoB

> [참고]
> 본 가이드는 JEUS 8 Fxi#1, WebtoB 5 Fix4  버전을 기준으로 작성되었습니다.
> 다른 버전을 사용하시는 경우 해당 버전에 맞게 변경해 주십시오.

각 이미지 스크립트는 JDK 설치 후 DAS, MS, WebtoB를 설치합니다.
설치 이후의 설정이나 제어 방법은 TmaxSoft의 가이드 문서([JEUS](https://technet.tmaxsoft.com/upload/download/online/jeus/pver-20190227-000001/index.html), [WebtoB](https://technet.tmaxsoft.com/upload/download/online/webtob/pver-20201021-000001/index.html))를 참고하시기 바랍니다.

### 이미지 설치

JDK는 `~/apps/jdk8u292`에 설치되며, 해당 디렉토리에서 `~/apps/jdk8`로 링크가 생성됩니다.
JDK 설치 과정에서 `.bash_profile`의 `PATH`에 `~/apps/jdk8/bin` 경로가 추가됩니다.
이미 `~/apps/jdk8` 디렉토리가 있다면 JDK가 설치되지 않습니다.

#### JEUS DAS, MS

JEUS는 `~/apps/jeus8`에 설치됩니다.


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

WebtoB는 `~/apps/webtob` 에 설치됩니다.

### JEUS, WebtoB 기동 확인

#### JEUS

JEUS를 설정하거나 제어하려면 노드 매니저를 기동한 후 WebAdmin이나 jeusadmin을 통해서 제어해야 합니다.

##### 노드 매니저 기동

셸로 접속하여 startNodeManager 명령어로 노드 매니저를 실행합니다.
노드 매니저끼리 통신이 필요하므로 보안 그룹에 기본 포트인 7730에 대한 허용 규칙을 추가해야 합니다.

##### JEUS 기동

DAS는 startDomainAdminServer 명령어로 실행합니다.
```
startDomainAdminServer -uadministrator -pjeusadmin
```

##### JEUS WebAdmin

다음과 같이 WebAdmin을 실행합니다.

1. DAS가 설치된 인스턴스에 플로팅 IP를 설정합니다.
2. 해당 인스턴스의 보안 그룹에 9736 포트에 대한 허용 규칙을 추가합니다.
3. 웹 브라우저에서 `http://{플로팅 IP}:9736/webadmin`으로 접속하면 WebAdmin 화면을 볼 수 있습니다.


#### WebtoB

wscfl 명령어를 이용하여 설정 파일을 컴파일합니다.
```
wscfl -i http.m
```

wsboot를 이용하여 WebtoB를 기동합니다.
```
wsboot
```

wsadmin을 이용하여 상태를 확인하거나 제어할 수 있습니다.

## Apache Tomcat

### 기본 위치
Tomcat의 설치 경로는 아래와 같습니다.

```
~/apps/apache-tomcat-{버전}/
```

### Tomcat 시작/정지 방법

Tomcat은 초기 설치 과정에서 기본적으로 서비스로 등록이 되어, 인스턴스 시작 시 자동으로 실행됩니다. Tomcat을 수동으로 시작하거나 정지하기 하기 위해 아래 명령어를 사용할 수 있습니다.

``` sh
#tomcat 서비스 시작
shell> sudo systemctl start tomcat

#tomcat 서비스 정지
shell> sudo systemctl stop tomcat

#tomcat 서비스 재시작
shell> sudo systemctl restart tomcat
```

### Tomcat 기본 페이지 접속
Tomcat은 초기 설치 시 기본 포트인 8080으로 실행됩니다. 다음 명령어를 실행하면 Tomcat 기본 페이지에 접근할 수 있습니다.

```sh
shell> curl -i http://127.0.0.1:8080
HTTP/1.1 200
Content-Type: text/html;charset=UTF-8
...
```

### Tomcat 인스턴스 생성 후 초기 설정

#### 1\. 포트\(port\) 변경
초기 설치 시 기본 설정으로 실행됩니다. 보안상 포트 변경을 권장합니다.

##### 1) `server.xml` 파일 수정

`~/apps/apache-tomcat-{버전}/conf/server.xml` 파일을 열어서 \<Connector\> 부분에 아래와 같이 변경할 포트 주소를 입력합니다.

```sh
shell> vi ~/apps/apache-tomcat-{버전}/conf/server.xml
```

```xml
...
<Connector port="{변경할 포트 주소}" protocol="HTTP/1.1"
            connectionTimeout="20000"
            redirectPort="8443" />
...
```

##### 2) 서비스 재시작
포트 변경이 적용되도록 Tomcat 서비스를 재시작합니다.
```
shell> sudo systemctl restart tomcat
```

## Node.js

### 기본 위치
Node.js의 설치 경로는 아래와 같습니다.

```
~/apps/node-{버전}/
```

### Node 실행 방법

```sh
# app.js 예제 코드 작성
shell> echo "console.log('Hello World')" > app.js

# node 실행
shell> node app.js
Hello World
```

## Deep Learning Framework

### Deep Learning Framework 이미지 템플릿 생성

Deep Learning Framework를 사용하려면 먼저 이미지 템플릿을 생성해야 합니다.

서비스 선택 화면에서 **Compute > Image Builder > 이미지 템플릿 생성**으로 이동합니다.

**이미지 템플릿 이름**을 입력하고, OS는 **linux - Ubuntu - Server xx.xx LTS**를 선택하고, **최소 블록 스토리지**는 **70기가** 이상으로 지정합니다.

이렇게 선택하면, 아래 화면에 **Deep Learning Framework**가 보입니다.

해당 스크립트를 선택한 후, **확인** 버튼을 클릭합니다. 팝업이 나타나면 **생성** 버튼을 클릭합니다.

### Deep Learning Framework Instance 생성

이미지 빌드가 완료된 후 실제로 GPU Instance를 생성하기 위해 **GPU Instance** 버튼을 클릭하면 **Compute > GPU Instance > GPU Instance 생성**으로 이동합니다.

인스턴스 생성 시, 앞서 빌드하여 생성한 이미지를 선택하여 인스턴스를 생성합니다.

Deep Learning Framework Instance에서는 다음과 같은 소프트웨어가 제공됩니다.

* TensorFlow, PyTorch
* NVIDIA CUDA Toolkit, cuDNN, NCCL, TensorRT
* Intel MKL

NVIDIA cuDNN에는 NVIDIA Corporation에서 제공한 소스 코드가 포함되어 있습니다. [License](https://docs.nvidia.com/deeplearning/cudnn/sla/index.html)

설정을 완료한 후 인스턴스를 생성합니다. 인스턴스 생성에 대한 자세한 내용은 [Instance 개요](http://docs.toast.com/ko/Compute/Instance/ko/overview/)를 참고하시기 바랍니다.

### 설치된 개발 환경 확인

conda 명령어를 사용하여 Miniconda로 설치된 개발 환경을 확인합니다.

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

>[참고]
>
>더 자세한 사용법은 [Miniconda 문서](https://docs.conda.io/en/latest/miniconda.html)를 참고하세요.

### TensorFlow 사용 방법

먼저 TensorFlow 환경을 활성화합니다.

```
(base) root@b64e6a035884:~# conda activate tf2_py38
(tf2_py38) root@b64e6a035884:~#
```

다음과 같이 TensorFlow 훈련을 테스트합니다.

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
# 1개 이상의 GPU 사용 시 설정
NUM_GPUS=1 # 예) NUM_GPUS=2

python $HOME/models/official/vision/image_classification/mnist_main.py \
  --model_dir=$MODEL_DIR \
  --data_dir=$DATA_DIR \
  --train_epochs=2 \
  --distribution_strategy=mirrored \ # 1개 이상의 GPU를 사용 시 설정
  --num_gpus=$NUM_GPUS \ # 1개 이상의 GPU를 사용 시 설정
  --download

$ chmod +x train.sh
$ ./train.sh
```

>[참고]
>
>더 자세한 사용법은 [TensorFlow 튜토리얼](https://www.tensorflow.org/tutorials)을 참고하세요.

### PyTorch 사용 방법

먼저 PyTorch 환경을 활성화합니다.

```
(tf2_py38) root@b64e6a035884:~# conda deactivate
(base) root@b64e6a035884:~# conda activate pt_py38
(pt_py38) root@b64e6a035884:~#
```

다음과 같이 PyTorch 훈련을 테스트합니다.

```
$ cd ~/
$ git clone https://github.com/pytorch/examples.git
$ cd examples/mnist
$ python main.py --epochs 1
```

>[참고]
>
>더 자세한 사용법은 [PyTorch 튜토리얼](https://pytorch.org/tutorials/)을 참고하세요.


## Slurm

Slurm 설치 구성 요소는 Munge 패키지 설치 및 설정, 그리고 Slurm 패키지 설치까지 진행합니다. 실행을 위한 자세한 방법은 [Slurm Installation Guide](https://slurm.schedmd.com/quickstart_admin.html)를 참고하세요.

>[참고]
>
>Slurm 가이드에 있는 명령어는 모두 root 권한으로 실행해야 합니다.

### 준비

#### 1. `hosts` 파일 수정

`/etc/hosts` 파일을 열어서 클러스터 환경에 구성할 node의 IP와 별칭을 입력합니다.

``` console
# vi /etc/hosts
```

#### 2. `hostname` 파일 수정

`/etc/hostname` 파일을 열어서 현재 node의 별칭을 `hosts` 파일과 일치시킵니다.

``` console
# vi /etc/hostname
```


### 클러스터 구성 및 Slurm 설정

초기에 적용된 기본 설정이 없으므로, 직접 설정파일을 작성해야 합니다. [Slurm Configuration Guide](https://slurm.schedmd.com/quickstart_admin.html#Config)와 [Slurm Configuration Tool](https://slurm.schedmd.com/configurator.html)을 참고하여 작성 후, `/etc/slurm/slurm.conf` 파일에 저장합니다.

로그 파일의 경로는 `/var/log/slurm/` 경로 아래로 지정하여야 합니다. 다른 경로 지정을 원할 경우, configuration 파일에 경로를 명시하고 해당 directory의 소유자를 `SlurmUser` 설정값과 일치시켜야 합니다.


### Slurm 실행

클러스터를 모두 구성하고 구성 정보를 설정해야 실행할 수 있습니다. [Slurm Installation Guide](https://slurm.schedmd.com/quickstart_admin.html)와 [Slurm Quick Start Guide](https://slurm.schedmd.com/quickstart.html)를 참고하세요.


## NHN Kubernetes Service(NKS) Worker Node

NHN Kubernetes Service(NKS)의 워커 노드로 활용 가능한 이미지를 생성할 수 있습니다. 자세한 내용은 [NKS 사용자 가이드](/Container/NKS/ko/user-guide/#_24)를 참고하세요.