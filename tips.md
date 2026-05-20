# ログのリアルタイム確認

```
# Laravelアプリのログをリアルタイムで流す
docker compose exec app tail -f storage/logs/laravel.log

# Nginxのアクセスログを確認
docker compose logs -f web
```

# キャッシュのクリア

```
# ビューキャッシュのクリア
docker compose exec app php artisan view:clear

# 設定キャッシュのクリア
docker compose exec app php artisan config:clear

# ルートキャッシュのクリア
docker compose exec app php artisan route:clear
```

# メール確認ツール（Mailpit）

```
📮 Mailpit を使う理由（Mailtrap との違い）
Laravelの他のレッスンでは Mailtrap（クラウドサービス）を使ってメールを確認しています。しかし Docker 環境では、すべてをローカルコンテナで完結させるために Mailpit を使います。

Mailpit はコンテナとして起動するローカルの SMTP サーバーで、アカウント登録が不要です。Docker Compose に数行追加するだけで使えます。

以前は Mailhog が同じ用途で広く使われていましたが、現在はメンテナンスが停止しているため、後継の Mailpit が推奨されています。
```

## docker-compose.yml に追加

```
mailpit:
    image: axllent/mailpit
    ports:
      - "8025:8025"   # Web UI
      - "1025:1025"   # SMTPポート
    networks:
      - laravel-net
```

## .env の設定

```
MAIL_MAILER=smtp
MAIL_HOST=mailpit
MAIL_PORT=1025
MAIL_FROM_ADDRESS=example@example.com
MAIL_FROM_NAME="Example"
```

- ブラウザで http://localhost:8025 を開くと、送信されたメールを確認できます。

# phpMyAdmin でデータベースを確認する

## docker-compose.yml に追加

```
phpmyadmin:
    image: phpmyadmin/phpmyadmin
    ports:
      - "8888:80"
    environment:
      PMA_HOST: db
      MYSQL_ROOT_PASSWORD: secret
    networks:
      - laravel-net
```

- 追加後に docker compose up -d を実行すると、http://localhost:8888 でphpMyAdminにアクセスできます。

# 構成ファイルの整理（infra/ ディレクトリ）【任意】

```
docker-sample-app/
├── Dockerfile
├── docker-compose.yml
├── infra/
│   ├── nginx/
│   │   └── default.conf
│   └── mysql/
│       └── my.cnf        ← カスタム設定が必要な場合
├── app/
└── .env
```