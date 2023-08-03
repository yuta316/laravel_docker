# ベースイメージ(PHP 8.1の公式Dockerイメージ)
FROM php:8.1-fpm

# ローカルのphp.ini(PHPの設定ファイル)をコンテナにコピー
COPY ./php.ini /usr/local/etc/php/php.ini

# パッケージインストール
RUN apt-get update \
  && apt-get install -y git zip unzip vim

# LaravelがMySQLデータベースと連携するための拡張モジュールをインストール
RUN docker-php-ext-install pdo_mysql

# Node.jsとnpmのインストール
RUN apt-get install -y curl \
    && curl -fsSL https://deb.nodesource.com/setup_16.x | bash - \
    && apt-get install -y nodejs
    
# Composerをインストール
ENV COMPOSER_ALLOW_SUPERUSER 1
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# Laravelアプリケーションのディレクトリを設定
WORKDIR /var/www/html
