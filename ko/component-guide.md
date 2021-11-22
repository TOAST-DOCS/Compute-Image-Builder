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

``` sh
# MySQL 서비스 시작
shell> sudo systemctl start mysqld

# MySQL 서비스 종료
shell> sudo systemctl stop mysqld

# MySQL 서비스 재시작
shell> sudo systemctl restart mysqld
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

초기 설치 후 MySQL root 계정 비밀번호는 지정되어 있지 않습니다. 그러므로 설치 후 반드시 비밀번호를 설정해야 합니다.

```
SET PASSWORD [FOR user] = password_option

mysql> SET PASSWORD = PASSWORD('비밀번호');
```

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
shell> mysql -u root
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

## Deep Learning Framework

### Deep Learning Instance 생성

Deep Learning Framework를 사용하려면 먼저 GPU Instance를 생성해야 합니다.

**GPU Instance** 버튼을 클릭하면 **Compute > GPU Instance > GPU Instance 생성**으로 이동합니다.

인스턴스 생성 시 **Deep Learning Instance**를 선택하여 인스턴스를 생성합니다.

Deep Learning Instance에서는 다음과 같은 버전의 소프트웨어가 제공됩니다.

| 소프트웨어 | 버전 | 설치 방식 |
| --- | --- | --- | 
| TensorFlow | 2.4.1 | pip, [참조](https://www.tensorflow.org/install/pip) |
| PyTorch | 1.7.1 | conda, [참조](https://pytorch.org/get-started/previous-versions/) |
| Python | 3.8.11 | conda |
| OS | Ubuntu 18.04 LTS | n/a |
| NVIDIA Driver | 450.102.04 | apt |
| NVIDIA CUDA | 11.0 | apt |
| NVIDIA cuDNN | 8.0.4 | apt |
| NVIDIA NCCL | 2.7.8 | apt |
| NVIDIA TensorRT | 7.1.3 | apt |
| Intel oneAPI MKL | 2021.4.0 | apt |

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
