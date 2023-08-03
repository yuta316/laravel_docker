# ベースイメージ(Nginxの公式Dockerイメージ)
FROM nginx:stable-alpine

# ローカルのnginx.conf(Nginxのカスタム設定ファイル)をコンテナにコピー
COPY ./nginx.conf /etc/nginx/conf.d/default.conf