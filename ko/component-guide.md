## Compute > Image Builder > 설치 구성 요소 가이드

### CUBRID

#### CUBRID 서비스 시작/정지 방법

“cubrid” Linux 계정으로 로그인하여 CUBRID 서비스를 다음과 같이 사용가능합니다.
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
#### CUBRID 접속

이미지 생성 후 초기에는 아래와 같이 접속합니다.
“cubrid” Linux 계정으로 로그인하여 CUBRID 서비스를 다음과 같이 시작합니다.

``` sh
shell> sudo su - cubrid
shell> csql -u dba demodb@localhost
```

#### CUBRID 인스턴스 생성 후 초기 설정

##### 1\. 비밀번호 설정

초기 설치 후 cubrid dba 계정 비밀번호는 지정되어 있지 않습니다.  그러므로 설치 후 반드시 비밀번호를 설정해야 합니다.

```
shell> csql -u dba -c "ALTER USER dba PASSWORD 'new_password'" demodb@localhost
```

##### 2\. broker 포트\(port\) 변경

**query\_editor**의 브로커 포트는 기본값이 **30000**으로 설정되며,  **broker1**의 브로커 포트는 기본값이 **33000**으로 설정됩니다. 
보안상 포트 변경을 권장합니다.

###### 1)  broker  파일 수정

/opt/cubrid/conf/cubrid\_broker.conf  파일을 열어서 아래와 같이 변경할 포트 주소를 입력합니다.

```
shell> vi /opt/cubrid/conf/cubrid_broker.conf
```

```
[%query_editor]
BROKER_PORT             =[변경할 port 주소]

[%BROKER1]
BROKER_PORT             =[변경할 port 주소]
```

###### 2) broker 재시작

```
shell> cubrid broker restart 
```

