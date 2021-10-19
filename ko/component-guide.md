## Compute > Image Builder > 설치 구성 요소 가이드


### MariaDB
#### MariaDB 시작/정지 방법
``` sh
# MariaDB 서비스 시작
shell> sudo systemctl start mariadb.service

# MariaDB 서비스 종료
shell> sudo systemctl stop mariadb.service

# MariaDB 서비스 재시작
shell> sudo systemctl restart mariadb.service
```

#### MariaDB 접속
이미지 생성 후 초기에는 아래와 같이 접속합니다.
``` sh
shell> mysql -u root
```

패스워드 변경 후에는 아래와 같이 접속합니다.
``` sh
shell> mysql -u root -p
Enter password:
```

#### MariaDB 인스턴스 생성 후 초기 설정
##### 1. 비밀번호 설정
초기 설치 후 MariaDB root 계정 비밀번호는 지정되어 있지 않습니다. 그러므로 설치 후 반드시 비밀번호를 설정해야 합니다.
```
SET PASSWORD [FOR user] = password_option

MariaDB> SET PASSWORD = PASSWORD('비밀번호');
```

##### 2. 포트(port) 변경
초기 설치 후 포트는 MariaDB의 기본 포트인 3306입니다. 보안상 포트 변경을 권장합니다.
###### 1) `/etc/my.cnf.d/servfer.cnf` 파일 수정
`/etc/my.cnf.d/server.cnf` 파일을 열어서 [mariadb] 밑에 아래와 같이 변경할 포트 주소를 입력합니다.
```
shell> sudo vi /etc/my.cnf.d/server.cnf
```

```
[mariadb]
port=[변경할 port 주소]
```
###### 2) 인스턴스 재시작
```
sudo systemctl restart mariadb.service
```

## MySQL
#### MySQL 시작/정지 방법
``` sh
# MySQL 서비스 시작
shell> sudo systemctl start mysqld.service

# MySQL 서비스 종료
shell> sudo systemctl stop mysqld.service

# MySQL 서비스 재시작
shell> sudo systemctl restart mysqld.service
```

#### MySQL 접속
이미지 생성 후 초기에는 아래와 같이 접속합니다.
``` sh
shell> mysql -u root
```

패스워드 변경 후에는 아래와 같이 접속합니다.
``` sh
shell> mysql -u root -p
Enter password:
```

#### MySQL 인스턴스 생성 후 초기 설정
##### 1. 비밀번호 설정
초기 설치 후 MySQL root 계정 비밀번호는 지정되어 있지 않습니다. 그러므로 설치 후 반드시 비밀번호를 설정해야 합니다.
```
SET PASSWORD [FOR user] = password_option

MySQL> SET PASSWORD = PASSWORD('비밀번호');
```
MySQL 기본 validate_password_policy는 아래와 같습니다.
- validate_password_policy=MEDIUM
- **기본 8자 이상, 숫자, 소문자, 대문자, 특수문자** 를 포함해야 함


##### 2. 포트(port) 변경
초기 설치 후 포트는 MySQL 기본 포트인 3306입니다. 보안상 포트 변경을 권장합니다.
###### 1) `/etc/my.cnf` 파일 수정
`/etc/my.cnf` 파일을 열어서 아래와 같이 변경할 포트 주소를 입력합니다.
```
shell> sudo vi /etc/my.cnf
```

```
port=[변경할 port 주소]
```

###### 2) 인스턴스 재시작
```
sudo systemctl restart mysqld.service
```
