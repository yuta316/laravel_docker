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
DB_USERNAME=作成するデータベースユーザ名(root以外の一般ユーザ名を指定)
DB_PASSWORD=作成するデータベースパスワード
```

3. コンテナ作成

```
sudo docker compose build
sudo docker compose up -d
```

4. ソースコード反映

```
git clone git@github.com:githubアカウント名/リポジトリ名.git
sudo mv リポジトリ名/* ./src/
sudo mv リポジトリ名/.[^\.]* src/
```

5. 各種設定

```
sudo docker compose exec php composer install
sudo docker compose exec php cp .env.example .env
sudo docker compose exec php vim .env
```

env にて以下の内容を記載しておく

```
APP_NAME="アプリ名"
APP_ENV=production
APP_KEY=この後生成
APP_DEBUG=false
APP_URL="アプリURL(http://13.114.168.100/等EC2のパブリックIP)"

中略

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=作成したデータベース名
DB_USERNAME=作成したデータベースユーザ名
DB_PASSWORD=作成したデータベースパスワード
```

```
sudo docker compose exec php php artisan key:generate
sudo docker compose exec php php artisan storage:link
sudo docker compose exec php chmod -R 777 storage bootstrap/cache -R
sudo docker compose exec php npm install
sudo docker compose exec php npm run build
sudo docker compose exec php php artisan migrate:fresh
```

6. ssh化(AmazonLinux2023の場合)
```
sudo dnf install -y python3 augeas-libs pip
sudo python3 -m venv /opt/certbot/
sudo /opt/certbot/bin/pip install --upgrade pip
sudo /opt/certbot/bin/pip install certbot
sudo docker comose down
```
Certbotの実行
```
sudo certbot certonly --standalone
```
メールアドレス、Y、N、ドメイン名の順に入力する
成功すると以下の様に証明書のパスが表示される。
```
Successfully received certificate.
Certificate is saved at: /etc/letsencrypt/live/ドメイン名/fullchain.pem
Key is saved at:         /etc/letsencrypt/live/ドメイン名/privkey.pem
This certificate expires on 2023-11-08.
These files will be updated when the certificate renews.
```
続いて生成したファイルのパスを記載する。
```
vim docker/nginx/nginx.conf
```
後半のserver{xxx}をコメントアウトし、ドメイン名を記載する
```
# server {
#   listen       443 ssl http2;
#   listen       [::]:443 ssl http2;
#   server_name      ドメイン名を記入;
#   root /var/www/html/public;
#   ssl_certificate "/etc/letsencrypt/live/ドメイン名/fullchain.pem";
#   ssl_certificate_key "/etc/letsencrypt/live/ドメイン名/privkey.pem";

#   index index.php;

#   # 文字セットをUTF-8に設定
#   charset utf-8;

#   # ルートへのリクエストを処理
#   # 該当するファイルが存在しない場合はindex.phpにリダイレクト
#   location / {
#     try_files $uri $uri/ /index.php?$query_string;
#   }

#   # PHPファイルへのリクエストを処理
#   location ~ \.php$ {
#     # PHP-FPM（FastCGI Process Manager）にリクエストを転送
#     # PHP-FPMはphp:9000で動作していると仮定
#     fastcgi_pass php:9000;
#     fastcgi_index index.php;
#     fastcgi_param SCRIPT_FILENAME  $document_root$fastcgi_script_name;
#     # FastCGIの設定パラメータをインクルード
#     include       fastcgi_params;
#   }
# }
```
再ビルド、コンテナ作成をすることで、サイトがSSL化される
```
sudo docker compose build
sudo docker compose up -d
```
