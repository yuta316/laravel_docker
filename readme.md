# 使い方

## EC2 の作成

1. EC2 インスタンスの作成
2. docker インストール

```
sudo yum install -y docker
sudo service docker start
sudo usermod -a -G docker ec2-user
```

3. docker-comopse のインストール

```
sudo mkdir -p /usr/local/lib/docker/cli-plugins
sudo curl -SL https://github.com/docker/compose/releases/download/v2.4.1/docker-compose-linux-x86_64 -o /usr/local/lib/docker/cli-plugins/docker-compose
sudo chmod +x /usr/local/lib/docker/cli-plugins/docker-compose
```

4. git のインストール

```
sudo yum install git -y
cd ~/.ssh
ssh-keygen
sudo cat id_rsa.pub
```

表示された公開鍵を Github に登録する

## コンテナの作成

1. ソースコードのクローン

```
cd ~
git clone git@github.com:yuta316/laravel_docker.git
cd laravel_docker
```

2. env ファイル作成

```
cp .env.example .env
vi .env
```

以下のように編集

```
APP_DEBUG=true
APP_ENV=production
APP_URL=http://localhost
LOG_CHANNEL=stderr
LOG_STDERR_FORMATTER=LOG_STDERR_FORMATTER:-Monolog\Formatter\JsonFormatter
DB_CONNECTION=mysql
DB_HOST=db
DB_PORT=3306
DB_DATABASE=作成するデータベース名
DB_USERNAME=作成するデータベースユーザ名
DB_PASSWORD=作成するデータベースパスワード
```

3. コンテナ作成

```
sudo docker compose build
sudo docker compose up -d
```
