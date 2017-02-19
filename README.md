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
docker run --name mysqld -e MYSQL_DATABASE=sample_db -e MYSQL_UESR=test_user -e MYSQL_PASSWORD=test_user -e MYSQL_ROOT_PASSWORD=verysecret -d mysql
```
