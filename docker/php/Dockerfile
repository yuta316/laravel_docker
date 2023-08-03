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
ENV NODE_VERSION=16.14.0
RUN curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.34.0/install.sh | bash
ENV NVM_DIR=/root/.nvm
RUN . "$NVM_DIR/nvm.sh" && nvm install ${NODE_VERSION}
RUN . "$NVM_DIR/nvm.sh" && nvm use v${NODE_VERSION}
RUN . "$NVM_DIR/nvm.sh" && nvm alias default v${NODE_VERSION}
ENV PATH="/root/.nvm/versions/node/v${NODE_VERSION}/bin/:${PATH}"

# Composerをインストール
ENV COMPOSER_ALLOW_SUPERUSER 1
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# Laravelアプリケーションのディレクトリを設定
WORKDIR /var/www/html
