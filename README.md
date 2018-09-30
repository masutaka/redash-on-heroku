# Redash on Heroku

Dockerfiles for hosting redash on heroku

## How to deploy

```sh
git clone git@github.com:willnet/redash-on-heroku.git
cd redash-on-heroku
heroku create your_app_name
heroku container:push --recursive
```

## How to setup

### Add Addons

Add following addons on heroku dashboard.

- heroku postgres

    ```sh
    heroku addons:create heroku-postgresql:hobby-dev
    ```

- Redis Cloud(or something)

    ```sh
    heroku addons:create rediscloud:30
    ```

- sendgrid (or something)

    ```sh
    heroku addons:create sendgrid:starter
    ```

Choose redis addon allow more than or equal 30 connections. Otherwise you will get connection errors frequently.

### Add environment variables

Add environment variables like following.

```sh
heroku config:set PYTHONUNBUFFERED=0
heroku config:set QUEUES=queries,scheduled_queries,celery
heroku config:set REDASH_COOKIE_SECRET=YOUR_SECRET_TOKEN
heroku config:set REDASH_DATABASE_URL=$(heroku config:get DATABASE_URL)
heroku config:set REDASH_LOG_LEVEL=INFO
heroku config:set REDASH_REDIS_URL=$(heroku config:get REDISCLOUD_URL)
heroku config:set REDASH_MAIL_PASSWORD=YOUR_ADDON_PASSWORD
heroku config:set REDASH_MAIL_PORT=587
heroku config:set REDASH_MAIL_SERVER=YOUR_ADDON_DOMAIN
heroku config:set REDASH_MAIL_USERNAME=YOUR_ADDON_USERNAME
heroku config:set REDASH_MAIL_USE_TLS=true
heroku config:set REDASH_MAIL_DEFAULT_SENDER=YOUR_MAIL_ADDRESS
```

See also https://redash.io/help/open-source/setup#-setup

### Release container

```sh
heroku container:release web worker
```

### Create database

After deploy and add postgres addon, create database like following.

```sh
heroku run /app/manage.py database create_tables
```

### Enable worker dyno

```sh
heroku ps:scale worker=1
```
