## Compute > Image Builder > インストールコンポーネントガイド

## PostgreSQL

### PostgreSQL開始/停止方法

```
#postgresqlサービス開始
shell> systemctl start postgresql-${version}

#postgresqlサービス中止
shell> systemctl stop postgresql-${version}

#postgresqlサービス再起動
shell> systemctl restart postgresql-${version}
```

### PostgreSQL接続

インスタンスを作成した後、初めに以下のように接続します。
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
shell> vi /var/lib/pgsql/${version}/data/postgresql.conf


#postgresql.confファイルに使用するポートを入力します。

port =使用するポート名


#viエディタ保存


#postgresqlサービス再起動

shell> systemctl restart postgresql-${version}


#変更されたポートで以下のように接続

shell> psql -p[変更されたポート番号]
```

#### 2\. サーバーログタイムゾーン変更

サーバーログに記録される基本時間帯がUTCに設定されています。 SYSTEMローカル時間と同じに変更することを推奨します。
<br>
```
shell> vi /var/lib/pgsql/${version}/data/postgresql.conf


#postgresql.confファイルに使用するタイムゾーンを入力します。

log_timezone =使用するタイムゾーン


#viエディタ保存


#postgresqlサービス再起動

shell> systemctl restart postgresql-${version}


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
shell> vi /var/lib/pgsql/${version}/data/postgresql.conf


#postgresql.confファイルに許可するアドレスを指定してください。
#IPv4アドレスを全て許可する場合0.0.0.0
#IPv6アドレスを全て許可する場合::
#すべてのアドレスを許可する場合 *

listen_addresses =許可するアドレス


#viエディタ保存


shell> vi /var/lib/pgsql/${version}/data/pg_hba.conf


#IPアドレス形式ごとにクライアント認証制御
#古いクライアントライブラリはscram-sha-256方式をサポートしていないため、md5に変更必要

# TYPE  DATABASE        USER            ADDRESS                 METHOD
# IPv4 local connections:
host    all             all             127.0.0.1/32            scram-sha-256
host  許可DB        許可ユーザー    許可アドレス               scram-sha-256
# IPv6 local connections:
host    all             all             ::1/128                 scram-sha-256
host  許可DB        許可ユーザー    許可アドレス               scram-sha-256


#postgresqlサービス再起動

shell> systemctl restart postgresql-${version}
```

### PostgreSQLディレクトリの説明

PostgreSQLディレクトリおよびファイルの説明は以下の通りです。

| 名前 | 説明 |
| --- | --- |
| postgresql.cnf | /var/lib/pgsql/{version}/data/postgresql.cnf |
| initdb.log | PostgreSQLデータベースクラスタ作成log - /var/lib/pgsql/{version}/initdb.log |
| DATADIR | PostgreSQLデータファイルパス - /var/lib/pgsql/{version}/data/ |
| LOG | PostgreSQL logファイルパス - /var/lib/pgsql/{version}/data/log/\*.log |

## MySQL

### MySQL起動/停止方法

``` sh
# MySQLサービスの起動
shell> sudo systemctl start mysqld
# MySQLサービスの終了
shell> sudo systemctl stop mysqld
# MySQLサービスの再起動
shell> sudo systemctl restart mysqld
```

### MySQL接続

イメージ作成後、最初は以下のように接続します。

``` sh
shell> mysql -u root
```

パスワード変更後は以下のように接続します。

``` sh
shell> mysql -u root -p
Enter password:
```

### MySQLインスタンス作成後の初期設定

#### 1\.パスワード設定

初期インストール後、MySQL ROOTアカウントパスワードは指定されていません。したがってインストール後、すぐにパスワードを設定する必要があります。

```
mysql> ALTER USER USER() IDENTIFIED BY '新しいパスワード';
```

MySQL基本validate\_password\_policyは下記の通りです。

* validate\_password\_policy=MEDIUM
* 基本**8文字以上、数字、大文字、小文字、特殊文字**を含める必要がある

#### 2\. ポート\(port\)変更

初期インストール後、ポートはMySQLの基本ポートである3306です。セキュリティ上、ポートを変更することを推奨します。

##### 1) `/etc/my.cnf`ファイルの修正

`/etc/my.cnf`ファイルに使用するポートを指定します。

```
shell> sudo vi /etc/my.cnf
```

```
port=[変更するportアドレス]
```

##### 2)インスタンスの再起動
ポートの変更が適用されるようにインスタンスを再起動します。
```
sudo systemctl restart mysqld
```

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
shell> sudo mysql -u root
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

## Apache Kafka
> [参考]
> このガイドはKafka 3.3.1バージョンを基準に作成されました。
> 他のバージョンを使用する場合は、該当のバージョンに合わせて変更してください。
> インスタンスタイプはc1m2(CPU 1core、Memory 2GB)以上の仕様で作成してください。

### Zookeeper、Kafka broker起動/停止
```
# Zookeeper、Kafka broker起動(Zookeeperを先に起動)
shell> sudo systemctl start zookeeper.service
shell> sudo systemctl start kafka.service
# Zookeeper、Kafka broker終了(Kafka brokerを先に終了)
shell> sudo systemctl stop kafka.service
shell> sudo systemctl stop zookeeper.service
# Zookeeper, Kafka broker再起動
shell> sudo systemctl restart zookeeper.service
shell> sudo systemctl restart kafka.service
```

### Kafka Clusterインストール
'- 必ず新規インスタンスにインストールします。
- インスタンスは3台以上、奇数で必要です。インスタンス1台でインストールスクリプトを実行します。
- インスタンス1台にkafka broker、zookeeper nodeが各1つずつ構成されます。
- インストールスクリプトを実行するインスタンスの ~ パスに他のインスタンスに接続する時に必要なキーペア(PEMファイル)が必要です。クラスタインスタンスのキーペアはすべて同じでなければなりません。
'- デフォルトポートのインストールのみサポートします。ポートの変更が必要な場合はクラスタインストールを完了してから初期設定ガイドのポート変更を参考にして変更します。
'- インスタンス間のKafka関連ポート通信のために、以下のセキュリティグループ設定を追加します。

セキュリティグループ設定
```
方向：受信
IPプロトコル： TCP
ポート： 22, 9092, 2181, 2888, 3888
```
Hostname、IPの確認方法
```
# Hostname確認
shell> hostname
# IP確認
コンソール画面
またはshell> hostname -i
```
Clusterインストールスクリプト実行例(上で確認したhostname、IPを入力)
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

### Apache Kafkaインスタンス作成後の初期設定
#### ポート(port)変更
最初のインストール後、ポートはKafkaデフォルトポート9092、Zookeeperデフォルトポート2181です。セキュリティのためにポートを変更することを推奨します。

##### 1) ~/kafka/config/zookeeper.propertiesファイル修正
~/kafka/config/zookeeper.propertiesファイルを開いてclientPortに変更するZookeeper portを入力します。
```
shell> vi ~/kafka/config/zookeeper.properties
clientPort=変更するzookeeper port
```
##### 2) ~/kafka/config/server.propertiesファイル修正
~/kafka/config/server.propertiesファイルを開いてlistenersに変更するKafka portを入力します。

インスタンスIPの確認方法
```
コンソール画面のPrivate IP
またはshell> hostname -i
```
```
shell> vi ~/kafka/config/server.properties

# コメント解除
listeners=PLAINTEXT://インスタンスIP：変更するkafka port

# Zookeeperポート変更
zookeeper.connect=インスタンスIP：変更するzookeeper port
---> クラスタの場合、各インスタンスIPのZookeeper portを変更
```

##### 3) Zookeeper、Kafka brokerの再起動
```
shell> sudo systemctl stop kafka.service
shell> sudo systemctl stop zookeeper.service
shell> sudo systemctl start zookeeper.service
shell> sudo systemctl start kafka.service
```

##### 4) Zookeeper、Kafka port変更確認
変更されたポートが使用されていることを確認します。
```
shell> netstat -ntl | grep [Kafka port]
shell> netstat -ntl | grep [Zookeeper port]
```

### Apache Kafkaトピックおよびデータ作成/使用

トピックの作成/照会
```
# インスタンスIP = Private IP / Kafka基本port = 9092
# トピック作成
shell> ~/kafka/bin/kafka-topics.sh --create --bootstrap-server [インスタンスIP]:[Kafka PORT] --topic kafka
# トピックリスト照会
shell> ~/kafka/bin/kafka-topics.sh --list --bootstrap-server [インスタンスIP]:[Kafka PORT]
# トピック詳細情報確認
shell> ~/kafka/bin/kafka-topics.sh --describe --bootstrap-server [インスタンスIP]:[Kafka PORT] --topic kafka
# トピック削除
shell> ~/kafka/bin/kafka-topics.sh --delete --bootstrap-server [インスタンスIP]:[Kafka PORT] --topic kafka
```
データ作成/使用
```
# producer起動
shell> ~/kafka/bin/kafka-console-producer.sh --broker-list [インスタンスIP]:[Kafka PORT] --topic kafka
# consumer起動
shell> ~/kafka/bin/kafka-console-consumer.sh --bootstrap-server [インスタンスIP]:[Kafka PORT] --from-beginning --topic kafka
```

## Redis

### Redis起動/停止
```
# Redisサービスの起動
shell> sudo systemctl start redis

# Redisサービスの停止
shell> sudo systemctl stop redis

# Redisサービスの再起動
shell> sudo systemctl restart redis
```

### Redis接続
`redis-cli`コマンドを利用してRedisインスタンスに接続できます。
```
shell> redis-cli
```

### Redisインスタンス作成後の初期設定
Redisインスタンスの基本設定ファイルは`~/redis/redis.conf`です。変更が必要なパラメータの説明は次のとおりです。

#### bind
- 基本値：`127.0.0.1 -::1`
- 変更値：`<private ip> 127.0.0.1 -::1`

Redisが使用するipの値です。サーバー外部からRedisインスタンスへのアクセスを許可するには該当パラメータにprivate ipを追加する必要があります。 private ipは`hostname -I`コマンドで確認できます。

#### port
- 基本値：`6379`

ポートはRedisデフォルト値である6379です。セキュリティ上、ポートを変更することを推奨します。ポートを変更した後は、以下のコマンドでRedisに接続できます。

```
shell> redis-cli -p <新しいポート>
```

#### requirepass/masterauth
- 基本値：`nhncloud`

基本パスワードは`nhncloud`です。セキュリティ上、パスワードを変更することを推奨します。複製接続を使用する場合、`requirepass`と`masterauth`値を同時に変更する必要があります。

### 自動HA構成スクリプト
NHN CloudのRedisインスタンスは自動的にHA環境を構成するスクリプトを提供します。スクリプトは必ず**インストール直後の新規インスタンス**でのみ使用することができ、redis.confで設定値を変更した場合には使用できません。

スクリプトを使用するには次の設定が必ず必要です。

##### キーペアコピー
インストールスクリプトを実行するインスタンスに他のインスタンス接続に必要なキーペア(PEMファイル)が必要です。キーペアは次のようにコピーできます。
- ubuntu
```
local> scp -i <キーペア>.pem <キーペア>.pem ubuntu@<floating ip>:/home/ubuntu/
```
作成したインスタンスのキーペアは、すべて同じである必要があります。

##### セキュリティグループ設定
Redisインスタンス間の通信のためにセキュリティグループ(**Network** > **Security Groups**)設定が必要です。以下のルールでセキュリティグループを作成し、Redisインスタンスに適用してください。

| 方向 | IPプロトコル| ポート範囲| Ether| 遠隔|
| --- | --- | --- | --- | --- |
| 受信|TCP | 6379| IPv4| インスタンスIP(CIDR)|
| 受信|TCP | 16379| IPv4| インスタンスIP(CIDR)|
| 受信|TCP | 26379| IPv4| インスタンスIP(CIDR)|

#### Sentinel自動構成
Sentinel構成のために3つのRedisインスタンスが必要です。マスターとして使用するインスタンスにキーペアをコピーし、以下のようにスクリプトを実行してください。

```
shell> sh .redis_make_sentinel.sh
```

その後、接続情報で使用するマスター名(Master Name)とマスター及びレプリカのprivate IPを順番に入力します。各インスタンスのprivate IPは`hostname -I`コマンドで確認できます。

```
shell> sh .redis_make_sentinel.sh
Enter Master's Name (ex> mymaster) : mymaster
Enter Master's IP: 192.168.0.33
Enter Replica-1's IP: 192.168.0.27
Enter Replica-2's IP: 192.168.0.97
```

コピーしたキーペアのファイル名を入力します。
```
shell> Enter Pemkey's name: <キーペア>.pem
```

#### Cluster自動構成
Cluster構成のために6つのRedisインスタンスが必要です。マスターとして使用するインスタンスにキーペアをコピーし、以下のようにスクリプトを実行してください。

```
shell> sh .redis_make_cluster.sh
```

その後、クラスタに使用するRedisインスタンスのprivate IPを順番に入力します。各インスタンスのprivate IPは`hostname -I`コマンドで確認できます。

```
shell> sh .redis_make_cluster.sh
Enter cluster-1'IP:  192.168.0.79
Enter cluster-2'IP:  192.168.0.10
Enter cluster-3'IP:  192.168.0.33
Enter cluster-4'IP:  192.168.0.116
Enter cluster-5'IP:  192.168.0.91
Enter cluster-6'IP:  192.168.0.32
```

コピーしたキーペアのファイル名を入力します。

```
shell> Enter Pemkey's name: <キーペア>.pem
```

`yes`を入力し、クラスタ構成を完了します。
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

### デフォルトの場所
Tomcatのインストールパスは以下の通りです。

```
~/apps/apache-tomcat-{バージョン}/
```

### Tomcatの起動/停止方法

Tomcatは初期インストール中にデフォルトでサービスとして登録され、インスタンス起動時に自動的に実行されます。 Tomcatを手動で起動または停止するには、以下のコマンドを使用できます。

``` sh
#tomcatサービスの開始
shell> sudo systemctl start tomcat

#tomcatサービスの停止
shell> sudo systemctl stop tomcat

#tomcatサービスの再起動
shell> sudo systemctl restart tomcat
```

### Tomcat基本ページ接続
Tomcatは最初のインストール時にデフォルトのポート8080で実行されます。次のコマンドを実行するとTomcat基本ページにアクセスできます。

```sh
shell> curl -i http://127.0.0.1:8080
HTTP/1.1 200
Content-Type: text/html;charset=UTF-8
...
```

### Tomcatインスタンス作成後の初期設定

#### 1\. ポート\(port\)変更
最初のインストール時にデフォルト設定で実行されます。セキュリティ上、ポートの変更を推奨します。

##### 1) `server.xml`ファイルの修正

`~/apps/apache-tomcat-{バージョン}/conf/server.xml`ファイルを開き\<Connector\> 部分に以下のように変更するポートアドレスを入力します。

```sh
shell> vi ~/apps/apache-tomcat-{バージョン}/conf/server.xml
```

```xml
...
<Connector port="{変更するポートアドレス}" protocol="HTTP/1.1"
            connectionTimeout="20000"
            redirectPort="8443" />
...
```

##### 2)サービス再起動
ポートの変更が適用されるようにTomcatサービスを再起動します。
```
shell> sudo systemctl restart tomcat
```

## Node.js

### デフォルトの場所
Node.jsのインストールパスは以下の通りです。

```
~/apps/node-{バージョン}/
```

### Node実行方法

```sh
# app.jsサンプルコードの作成
shell> echo "console.log('Hello World')" > app.js

# node実行
shell> node app.js
Hello World
```

## Deep Learning Framework

### Deep Learning Frameworkイメージテンプレートの作成

Deep Learning Frameworkを使用するには、まずイメージテンプレートを作成する必要があります。

サービス選択画面で**Compute > Image Builder > イメージテンプレートの作成**に移動します。

**イメージテンプレート名**を入力し、OSは**linux - Ubuntu - Server xx.xx LTS**を選択して**最小ブロックストレージは70GB以上**を指定します。

このように選択すると、下の画面に**Deep Learning Framework**が表示されます。

該当スクリプトを選択した後、 **確認**ボタンをクリックします。ポップアップが表示されたら**作成**ボタンをクリックします。

### Deep Learning Framework Instance作成

イメージビルドが完了した後、実際にGPU Instanceを作成するために**GPU Instance**ボタンをクリックすると**Compute > GPU Instance > GPU Instanceの作成**に移動します。

インスタンスの作成時、ビルドして作成したイメージを選択してインスタンスを作成します。

Deep Learning Framework Instanceでは次のソフトウェアが提供されます。

* TensorFlow、PyTorch
* NVIDIA CUDA Toolkit、cuDNN、NCCL、TensorRT
* Intel MKL

NVIDIA cuDNNにはNVIDIA Corporationで提供したソースコードが含まれています。 [License](https://docs.nvidia.com/deeplearning/cudnn/latest/reference/eula.html)

設定を完了した後、インスタンスを作成します。インスタンス作成の詳しい内容は[Instance概要](http://docs.toast.com/ko/Compute/Instance/ko/overview/)を参照してください。

### インストールされた開発環境の確認

condaコマンドを使用してMinicondaにインストールされた開発環境を確認します。

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

>[参考]
>
>より詳しい使用方法は[Miniconda文書](https://docs.conda.io/en/latest/miniconda.html)を参照してください。

### TensorFlowの使用方法

TensorFlow環境を有効にします。

```
(base) root@b64e6a035884:~# conda activate tf2_py38
(tf2_py38) root@b64e6a035884:~#
```

次のようにTensorFlowトレーニングをテストします。

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
# 1個以上のGPUを使用する時に設定
NUM_GPUS=1 # 例) NUM_GPUS=2
python $HOME/models/official/vision/image_classification/mnist_main.py \
  --model_dir=$MODEL_DIR \
  --data_dir=$DATA_DIR \
  --train_epochs=2 \
  --distribution_strategy=mirrored \ # 1個以上のGPUを使用する時に設定
  --num_gpus=$NUM_GPUS \ # 1個以上のGPUを使用する時に設定
  --download
$ chmod +x train.sh
$ python ./train.sh
```

>[参考]
>
>より詳しい使用方法は[TensorFlowチュートリアル](https://www.tensorflow.org/tutorials)を参考してください。

### PyTorch使用方法

PyTorch環境を有効にします。

```
(tf2_py38) root@b64e6a035884:~# conda deactivate
(base) root@b64e6a035884:~# conda activate pt_py38
(pt_py38) root@b64e6a035884:~#
```

次のようにPyTorchトレーニングをテストします。

```
$ cd ~/
$ git clone https://github.com/pytorch/examples.git
$ cd examples/mnist
$ python manin.py --epochs 1
```

>[参考]
>
>より詳しい使用方法は[PyTorchチュートリアル](https://pytorch.org/tutorials/)を参照してください。

## Slurm

Slurmインストールコンポーネントは、Mungeパッケージのインストールと設定、そしてSlurmパッケージのインストールまで行います。実行方法の詳細については[Slurm Installation Guide](https://slurm.schedmd.com/quickstart_admin.html)を参照してください。

>[参考]
>
>Slurmガイドにあるコマンドはすべてroot権限で実行する必要があります。

### 準備

#### 1. `hosts`ファイルの修正

`/etc/hosts`ファイルを開き、クラスタ環境に構成するnodeのIPとエイリアスを入力します。

``` console
# vi /etc/hosts
```

#### 2. `hostname`ファイルの修正

`/etc/hostname`ファイルを開き、現在nodeのエイリアスを`hosts`ファイルと一致させます。

``` console
# vi /etc/hostname
```


### クラスタの構成とSlurm設定

初期に適用された基本設定がないため、直接設定ファイルを作成する必要があります。 [Slurm Configuration Guide](https://slurm.schedmd.com/quickstart_admin.html#Config)と[Slurm Configuration Tool](https://slurm.schedmd.com/configurator.html)を参考にして作成した後、 `/etc/slurm/slurm.conf`ファイルに保存します。

ログファイルのパスは`/var/log/slurm/`パスの下に指定する必要があります。他のパスを指定したい場合は、 configurationファイルにパスを明記し、そのdirectoryの所有者を`SlurmUser`設定値と一致させる必要があります。


### Slurm実行

すべてのクラスタを構成し、構成情報を設定すると実行できます。 [Slurm Installation Guide](https://slurm.schedmd.com/quickstart_admin.html)と[Slurm Quick Start Guide](https://slurm.schedmd.com/quickstart.html)を参照してください。


## NHN Kubernetes Service(NKS) Worker Node

NHN Kubernetes Service(NKS)のワーカーノードとして活用可能なイメージを作成できます。詳細は[NKSユーザーガイド](https://docs.nhncloud.com/ja/Container/NKS/ja/user-guide/#_25)を参照してください。
