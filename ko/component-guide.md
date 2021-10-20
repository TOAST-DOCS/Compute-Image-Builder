## Compute > Image Builder > 설치 구성 요소 가이드

### PostgreSQL 

`본 가이드는 13 버전을 기준으로 작성되었습니다.`  
`다른 버전을 사용하시는 경우 해당 버전에 맞게 변경해 주십시오.`

#### PostgreSQL 시작/정지 방법

```
#postgresql 서비스 시작
shell> systemctl start postgresql-13

#postgresql 서비스 중지
shell> systemctl stop postgresql-13

#postgresql 서비스 재시작
shell> systemctl restart postgresql-13
```

#### PostgreSQL 접속

이미지 생성 후 초기에는 아래와 같이 접속합니다.
<br>
```
#postgres로 계정 전환 후 접속
shell> su - postgres
shell> psql
```

#### PostgreSQL 인스턴스 생성 후 초기 설정

#### 1\. 포트\(port\) 변경

제공되는 이미지 포트는 PostgreSQL 기본 포트인 5432입니다. 보안상 포트 변경을 권장합니다.
<br>
```
shell> vi /var/lib/pgsql/13/data/postgresql.conf


#postgresql.conf 파일에 사용하고자 하는 포트를 명시해 줍니다.

port =사용하고자 하는 포트명


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


#postgresql.conf 파일에 사용하고자 하는 타임 존을 명시해 줍니다.

log_timezone =사용하고자 하는 타임 존


#vi 편집기 저장


#postgresql 서비스 재시작

shell> systemctl restart postgresql-13


#postgresql 접속

shell> psql


#변경한 설정 확인

postgres=# SHOW log_timezone;
```

### 3\. public 스키마 권한 취소

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


#postgresql.conf 파일에 허용하고자 하는 주소를 명시해 줍니다.
#IPv4 주소를 모두 허용하는 경우 0.0.0.0
#IPv6 주소를 모두 허용하는 경우 ::
#모든 주소를 허용하는 경우 *

listen_addresses =허용하고자 하는 주소


#vi 편집기 저장


shell> vi /var/lib/pgsql/13/data/pg_hba.conf


#IP주소 형식별로 클라이언트 인증 제어
#오래된 클라이언트 라이브러리는 scram-sha-256 방식이 지원되지 않으므로 md5로 변경 필요

# TYPE  DATABASE        USER            ADDRESS                 METHOD
# IPv4 local connections:
host    all             all             127.0.0.1/32            scram-sha-256
host    허용DB           허용유저          허용주소                   scram-sha-256
# IPv6 local connections:
host    all             all             ::1/128                 scram-sha-256
host    허용DB           허용유저          허용주소                   scram-sha-256


#postgresql 서비스 재시작

shell> systemctl restart postgresql-13
```

#### PostgreSQL 디렉터리 설명

PostgreSQL 디렉터리 및 파일 설명은 아래와 같습니다.

| 이름 | 설명 |
| --- | --- |
| postgresql.cnf | /var/lib/pgsql/{version}/data/postgresql.cnf |
| initdb.log | PostgreSQL 데이터베이스 클러스터 생성 log - /var/lib/pgsql/{version}/initdb.log |
| DATADIR | PostgreSQL 데이터 파일 경로 - /var/lib/pgsql/{version}/data/ |
| LOG | PostgreSQL log 파일 경로 - /var/lib/pgsql/{version}/data/log/\*.log |
