# docker-command
## Ubuntu Settings
### sudoを使わない実行
ユーザをdockerグループに追加します（dockerグループがない場合は、新規に作成されます）。    
```
sudo usermod -aG docker $USER
```
その後、OSログアウト&ログインします。  
もし、設定が反映されない場合は、一度Dockerサービスを再起動します。
```
sudo service docker restart
```

## MySQLコンテナ
* 最新安定版のイメージを取得
```
docker pull mysql
```
or
```
docker pull mysql:latest
```

* 起動時にCREATE DATABASEする。
```
docker run --name mysqld -e MYSQL_DATABASE=sample_db -e MYSQL_USER=test_user -e MYSQL_PASSWORD=test_user -e MYSQL_ROOT_PASSWORD=verysecret -d mysql
```

* リンクコンテナからマスターコンテナに接続します。
```
docker run --link  mysqld:mysql -it --rm mysql bash
```
※linkは、dockerのversion1.9以降では推奨されない可能性が高い。今後はネットワーキングによる設定が主流になる見込み。

* mysqlに接続します。
```
mysql -u test_user -ptest_user -h $MYSQL_PORT_3306_TCP_ADDR sample_db
```
