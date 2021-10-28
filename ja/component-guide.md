## Compute > Image Builder > インストールコンポーネントガイド

## PostgreSQL 

> [参考]
> このガイドはPostgreSQL 13バージョンを基準に作成されました。
> 異なるバージョンを使用する場合は該当バージョンに合わせて変更してください。

### PostgreSQL開始/停止方法

```
#postgresqlサービス開始
shell> systemctl start postgresql-13

#postgresqlサービス中止
shell> systemctl stop postgresql-13

#postgresqlサービス再起動
shell> systemctl restart postgresql-13
```

### PostgreSQL接続

インスタンスを作成した後、始めに以下のように接続します。
<br>
```
#postgresにアカウント切り替え後に接続
shell> su - postgres
shell> psql
```

### PostgreSQLインスタンス作成後、初期設定

#### 1\. ポート\(port\)変更

提供されるイメージポートはPostgreSQL基本ポートの5432です。セキュリティ上、ポートの変更を推奨します。
<br>
```
shell> vi /var/lib/pgsql/13/data/postgresql.conf


#postgresql.confファイルに使用するポートを入力します。

port =使用するポート名


#viエディタ保存


#postgresqlサービス再起動

shell> systemctl restart postgresql-13


#変更されたポートで以下のように接続

shell> psql -p[変更されたポート番号]
```

#### 2\. サーバーログタイムゾーン変更

サーバーログに記録される基本時間帯がUTCに設定されています。 SYSTEMローカル時間と同じに変更することを推奨します。
<br>
```
shell> vi /var/lib/pgsql/13/data/postgresql.conf


#postgresql.confファイルに使用するタイムゾーンを入力します。

log_timezone =使用するタイムゾーン


#viエディタ保存


#postgresqlサービス再起動

shell> systemctl restart postgresql-13


#postgresql接続

shell> psql


#変更した設定の確認

postgres=# SHOW log_timezone;
```

#### 3\. publicスキーマ権限キャンセル

基本的にすべてのユーザーにpublicスキーマのCREATEおよびUSAGE権限を付与しているため、データベースに接続することができるユーザーはpublicスキーマからオブジェクトを作成できます。すべてのユーザーがpublicスキーマからオブジェクトを作成できないように権限の取り消しを推奨します。
<br>
```
#postgresql接続

shell> psql


#権限取り消しコマンド実行

postgres=# REVOKE CREATE ON SCHEMA public FROM PUBLIC;
```

#### 4\. 遠隔接続許可

ローカルホスト以外の接続を許可するにはlisten_addresses変数とクライアント認証設定ファイルを変更する必要があります。
<br>
```
shell> vi /var/lib/pgsql/13/data/postgresql.conf


#postgresql.confファイルに許可するアドレスを指定してください。
#IPv4アドレスを全て許可する場合0.0.0.0
#IPv6アドレスを全て許可する場合::
#すべてのアドレスを許可する場合 *

listen_addresses =許可するアドレス


#viエディタ保存


shell> vi /var/lib/pgsql/13/data/pg_hba.conf


#IPアドレス形式ごとにクライアント認証制御
#古いクライアントライブラリはscram-sha-256方式をサポートしていないため、md5に変更必要

# TYPE  DATABASE        USER            ADDRESS                 METHOD
# IPv4 local connections:
host    all             all             127.0.0.1/32            scram-sha-256
host   許可DB         許可ユーザー      許可アドレス                scram-sha-256
# IPv6 local connections:
host    all             all             ::1/128                 scram-sha-256
host   許可DB         許可ユーザー      許可アドレス                scram-sha-256


#postgresqlサービス再起動

shell> systemctl restart postgresql-13
```

### PostgreSQLディレクトリの説明

PostgreSQLディレクトリおよびファイルの説明は以下の通りです。

| 名前 | 説明 |
| --- | --- |
| postgresql.cnf | /var/lib/pgsql/{version}/data/postgresql.cnf |
| initdb.log | PostgreSQLデータベースクラスタ作成log - /var/lib/pgsql/{version}/initdb.log |
| DATADIR | PostgreSQLデータファイルパス - /var/lib/pgsql/{version}/data/ |
| LOG | PostgreSQL logファイルパス - /var/lib/pgsql/{version}/data/log/\*.log |

## MariaDB

### MariaDB起動/停止方法

``` sh
# MariaDBサービス開始
shell> sudo systemctl start mariadb.service

# MariaDBサービス終了
shell> sudo systemctl stop mariadb.service

# MariaDBサービス再起動
shell> sudo systemctl restart mariadb.service
```

### MariaDB接続

イメージ作成後、最初は以下のように接続します。

``` sh
shell> mysql -u root
```

パスワード変更後は以下のように接続します。

``` sh
shell> mysql -u root -p
Enter password:
```

### MariaDBインスタンス作成後の初期設定

#### 1\. パスワード設定

最初のインストール後、MariaDB rootアカウントパスワードは指定されていません。そのためインストール後に必ずパスワードを設定する必要があります。

```
SET PASSWORD [FOR user] = password_option

MariaDB> SET PASSWORD = PASSWORD('パスワード');
```

#### 2\. ポート\(port\)変更

最初のインストール後のポートはMariaDBの基本ポート3306です。セキュリティ上、ポートの変更を推奨します。

##### 1) `/etc/my.cnf.d/servfer.cnf`ファイル修正

`/etc/my.cnf.d/server.cnf`ファイルを開いて[mariadb]の下に以下のように変更するポートアドレスを入力します。

```
shell> sudo vi /etc/my.cnf.d/server.cnf
```

```
[mariadb]
port=[変更するportアドレス]
```

##### 2)インスタンス再起動
ポートの変更を適用するためにインスタンスを再起動します。
```
sudo systemctl restart mariadb.service
```

## CUBRID

### CUBRIDサービスの開始/停止方法

“cubrid” LinuxアカウントでログインしてCUBRIDサービスを次のように開始/終了できます。

``` sh
# CUBRIDサービス/サーバー起動
shell> sudo su - cubrid
shell> cubrid service start 
shell> cubrid server start demodb

# CUBRIDサービス/サーバー終了
shell> sudo su - cubrid
shell> cubrid server stop demodb
shell> cubrid service stop 

# CUBRIDサービス/サーバー再起動
shell> sudo su - cubrid
shell> cubrid server restart demodb
shell> cubrid service restart 

# CUBRIDブローカー開始/終了/再起動
shell> sudo su - cubrid
shell> cubrid broker start
shell> cubrid broker stop
shell> cubrid broker restart
```

### CUBRID接続

イメージ作成後、次のように接続します。

``` sh
shell> sudo su - cubrid
shell> csql -u dba demodb@localhost
```

### CUBRIDインスタンス作成後の初期設定

#### 1\. パスワード設定

最初のインストール後、CUBRID dbaアカウントパスワードは指定されていません。そのためインストール後に必ずパスワードを設定する必要があります。

```
shell> csql -u dba -c "ALTER USER dba PASSWORD 'new_password'" demodb@localhost
```

#### 2\. brokerポート\(port\)変更

**query\_editor**のブローカーポートはデフォルト値が**30000**に設定されていて、**broker1**のブローカーポートはデフォルト値が**33000**に設定されます。
セキュリティ上、ポートの変更を推奨します。

##### 1) brokerファイル修正

`/opt/cubrid/conf/cubrid\_broker.conf`ファイルを開いて以下のように変更するポートアドレスを入力します。

```
shell> vi /opt/cubrid/conf/cubrid_broker.conf
```

```
[%query_editor]
BROKER_PORT             =[変更するportアドレス]

[%BROKER1]
BROKER_PORT             =[変更するportアドレス]
```

##### 2) broker再起動
ポートの変更を適用するためにbrokerを再起動します。
```
shell> cubrid broker restart 
```

## Jeus, WebtoB

> [참고]
> 본 가이드는 Jeus8Fxi1, WebtoB5.0Sp0Fix4  버전을 기준으로 작성되었습니다.
> 다른 버전을 사용하시는 경우 해당 버전에 맞게 변경해 주십시오.

각 이미지 스크립트는 jdk 설치 후 das, ms, webtob 를 설치합니다.
설치 이후의 설정이나 제어 부분은 TmaxSoft의 가이드문서([Jeus](https://technet.tmaxsoft.com/upload/download/online/jeus/pver-20190227-000001/index.html), [WebtoB](https://technet.tmaxsoft.com/upload/download/online/webtob/pver-20201021-000001/index.html) )를 참고해야 합니다.

### 이미지 설치

jdk는 /home1/centos/apps/jdk8u292 에 설치되며 /home1/centos/apps/jdk8 로 링크를 생성합니다.
.bash_profile 의 PATH 에 /home1/centos/apps/jdk8/bin 경로를 추가합니다.
이미 /home1/centos/apps/jdk8 경로의 디렉토리가 있다면 설치하지 않습니다.

#### Jeus DAS, MS

Jeus는 /home1/centos/apps/jeus8 에 설치를 합니다.


설치 시 아래 속성들로 설정을 합니다.

| 구분 | 기본값 | 
| --- | --- |
| 도메인 이름 | jeus_domain |
| 웹 어드민 포트 | 9736 |
| 어드민 서버 이름 | adminServer |
| 어드민 유저 아이디 | administrator |
| 어드민 유저 비밀번호 | jeusadmin |
| 노드 매니저 | java |

#### WebtoB

WetoB는 /home1/centos/apps/webtob 에 설치를 합니다.

### 기동 확인

#### Jeus

설정이나 제어를 위해서는 노드 매니저를 기동 후 웹어드민이나 jeusadmin을 통해서 제어를 해야 합니다.

##### 노드 매니저 기동

쉘로 접속하여 startNodeManager 명령어로 실행을 합니다.
노드 매니저끼리 통신이 필요하기 때문에 기본 포트인 7730 에 대해 보안그룹에 허용 추가 해야 합니다.

##### Jeus 기동

DAS 는 startDomainAdminServer 명령어로 실행을 합니다.
```
startDomainAdminServer -uadministrator -pjeusadmin
```

##### Jeus WebAdmin

DAS가 설치된 인스턴스에 플롯팅 IP를 설정 후
웹브라우저에서 http://플롯팅IP:9736/webadmin 으로 접속하면
웹어드민 화면을 볼 수 있습니다.
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

