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

## Deep Learning Framework

### Deep Learning Instance作成

Deep Learning Frameworkを使用するには、GPU Instanceを作成する必要があります。

**GPU Instance** ボタンをクリックすると、**Compute > GPU Instance > GPU Instance作成**に移動します。

インスタンス作成時、**Deep Learning Instance**を選択してインスタンスを作成します。

Deep Learning Instanceでは次のバージョンのソフトウェアが提供されます。

| ソフトウェア | バージョン | インストール方式 |
| --- | --- | --- | 
| TensorFlow | 2.4.1 | pip, [参照](https://www.tensorflow.org/install/pip) |
| PyTorch | 1.7.1 | conda, [参照](https://pytorch.org/get-started/previous-versions/) |
| Python | 3.8.11 | conda |
| OS | Ubuntu 18.04 LTS | n/a |
| NVIDIA Driver | 450.102.04 | apt |
| NVIDIA CUDA | 11.0 | apt |
| NVIDIA cuDNN | 8.0.4 | apt |
| NVIDIA NCCL | 2.7.8 | apt |
| NVIDIA TensorRT | 7.1.3 | apt |
| Intel oneAPI MKL | 2021.4.0 | apt |

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
>より詳しい使用方法は[PyTorch チュートリアル](https://pytorch.org/tutorials/)を参照してください。

## JEUS, WebtoB

> [参考]
> このガイドはJEUS 8 Fxi#1, WebtoB 5 Fix4 バージョンを基準に作成されました。
> 他のバージョンを使用する場合はそのバージョンに合わせて変更してください。

各イメージスクリプトは、JDKのインストール後にDAS、MS、WebtoBをインストールします。
インストール後の設定や制御方法はTmaxSoftのガイド文書([JEUS](https://technet.tmaxsoft.com/upload/download/online/jeus/pver-20190227-000001/index.html), [WebtoB](https://technet.tmaxsoft.com/upload/download/online/webtob/pver-20201021-000001/index.html))を参照してください。

### イメージのインストール

JDKは`~/apps/jdk8u292`にインストールされ、そのディレクトリで`~/apps/jdk8`にリンクが作成されます。
JDKのインストール中に`.bash_profile`の`PATH`に`~/apps/jdk8/bin`パスが追加されます。
すでに`~/apps/jdk8`ディレクトリがある場合はJDKがインストールされません。

#### JEUS DAS, MS

JEUSは`~/apps/jeus8`にインストールされます。 (スクリプトなどで内部的に決められたディレクトリにインストールする場合)


インストールする時、以下のプロパティに設定されます。

| 区分 | デフォルト値 | 
| --- | --- |
| ドメイン名 | jeus_domain |
| WebAdminポート | 9736 |
| Adminサーバー名 | adminServer |
| AdminユーザーID | administrator |
| Adminユーザーパスワード | jeusadmin |
| ノードマネージャ | java |

#### WebtoB

WebtoBは`~/apps/webtob`にインストールされます。

### JEUS、WebtoB起動確認

#### JEUS

JEUSを設定または制御するにはノードマネージャーを起動した後、WebAdminまたはjeusadminを通して制御する必要があります。

##### ノードマネージャの起動

シェルに接続してstartNodeManagerコマンドでノード マネージャを実行します。
ノード マネージャ同士で通信が必要なため、セキュリティグループに基本ポートである7730の許可ルールを追加する必要があります。

##### JEUS起動

DASはstartDomainAdminServerコマンドで実行します。
```
startDomainAdminServer -uadministrator -pjeusadmin
```

##### JEUS WebAdmin

次のようにWebAdminを実行します。

1. DASがインストールされたインスタンスにFloating IPを設定します。
2. 該当インスタンスのセキュリティグループに9736ポートの許可ルールを追加します。
3. Webブラウザで`http://{Floating IP}:9736/webadmin`に接続するとWebAdmin画面を見ることができます。


#### WebtoB

wscflコマンドを利用して設定ファイルをコンパイルします。
```
wscfl -i http.m
```

wsbootを利用してWebtoBを起動します。
```
wsboot 
```

wsadminを利用して状態の確認と制御を行うことができます。

## Tomcat

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
