# docker-command
## Ubuntu Settings
### apt-get install
※2017.03.14時点の最新版1.12.6がインストールされる。
```
$ sudo apt-get install docker.io vim-syntax-docker
$ docker --version
```

### sudoを使わない実行
ユーザをdockerグループに追加します（dockerグループがない場合は、新規に作成されます）。    
```
$ sudo usermod -aG docker $USER
```

その後、OSログアウト&ログインします。  
もし、設定が反映されない場合は、一度Dockerサービスを再起動します。
```
$ sudo service docker restart
```

## docker用ディレクトリ作成
```
$ mkdir ~/docker
```

## MySQLコンテナ
MySQLの公式イメージのBashは日本語に対応していないので、日本語を入力したりペーストすると入力文字が消えてしまいます。  
そこで、localesパッケージを導入して、環境変数の設定お行い、さらにテキストエディターを導入するのであれば、$TERM も合わせて設定しておきます。

* ディレクトリ作成
```
$ mkdir ~/docker/mysql
```

* Dockerfile作成
```
FROM mysql:latest
ENV DEBIAN_FRONTEND noninteractive

RUN apt-get update && apt-get install -y locales --no-install-recommends && rm -rf /var/lib/apt/lists/*

RUN dpkg-reconfigure locales && locale-gen C.UTF-8 && /usr/sbin/update-locale LANG=C.UTF-8

ENV LC_ALL C.UTF-8
ENV TERM xterm

RUN { \
  echo '[mysqld]'; \
  echo 'character-set-server = utf8mb4'; \
  echo '[client]'; \
  echo 'default-character-set = utf8mb4'; \
} > /etc/mysql/conf.d/charset.cnf
```

* イメージを再構築
```
$ docker build -t [repogitory/]mysqld-img .
```

* 起動時にCREATE DATABASE
```
$ docker run -d -p 13306:3306 --name mysqld -e MYSQL_DATABASE=sample_db -e MYSQL_USER=test_user -e MYSQL_PASSWORD=test_user -e MYSQL_CHARSET=utf8 -e MYSQL_ROOT_PASSWORD=verysecret [repogitory/]mysqld-img
```

* コンテナ内に入る
```
$ docker exec -it mysqld /bin/bash
```

※下記は、必要な場合に実施。
* リンクコンテナからマスターコンテナに接続
```
$ docker run --link  mysqld:mysql -it --rm mysql bash
```
※linkは、dockerのversion1.9以降では推奨されない可能性が高い。今後はネットワーキングによる設定が主流になる見込み。

* mysqlに接続します。
```
$ mysql -u test_user -ptest_user -h127.0.0.1 sample_db
```
