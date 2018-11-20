# Redash on Heroku

Dockerfiles for hosting redash on heroku

## How to deploy

```sh
$ git clone git@github.com:willnet/redash-on-heroku.git
$ cd redash-on-heroku
$ heroku update beta
$ heroku plugins:install @heroku-cli/plugin-manifest
$ heroku create masutaka-redash-heroku-yml --manifest
Reading heroku.yml manifest... done
Creating ⬢ masutaka-redash-heroku-yml... done, stack is container
Adding heroku-postgresql:hobby-dev... done
Adding rediscloud:30... done
Adding sendgrid:starter... done
Setting config vars... done
https://masutaka-redash-heroku-yml.herokuapp.com/ | https://git.heroku.com/masutaka-redash-heroku-yml.git
$ git push heroku trial-heroku-yml:master
```

## How to setup

### Add other environment variables

Add environment variables like following.

```sh
$ heroku config:set REDASH_COOKIE_SECRET=YOUR_SECRET_TOKEN
$ heroku config:set REDASH_DATABASE_URL=$(heroku config:get DATABASE_URL)
$ heroku config:set REDASH_HOST=YOUR_DOMAIN_URL
$ heroku config:set REDASH_MAIL_DEFAULT_SENDER=YOUR_MAIL_ADDRESS
$ heroku config:set REDASH_MAIL_PASSWORD=$(heroku config:get SENDGRID_PASSWORD)
$ heroku config:set REDASH_MAIL_USERNAME=$(heroku config:get SENDGRID_USERNAME)
$ heroku config:set REDASH_REDIS_URL=$(heroku config:get REDISCLOUD_URL)
```

See also https://redash.io/help/open-source/setup#-setup

### Create database

After deploy and add postgres addon, create database like following.

```sh
$ heroku run /app/manage.py database create_tables
```

### Enable worker dyno

```sh
$ heroku ps:scale worker=1
```

## Switch back to the stable update stream

You can switch back to the stable update stream and remove the plugin at any time:

```sh
$ heroku update stable
$ heroku plugins:remove manifest
```

## How to upgrade

```sh
$ heroku ps:scale web=0 worker=0
$ heroku container:push --recursive
$ heroku container:release web worker
$ heroku run /app/manage.py db upgrade
$ heroku ps:scale web=1 worker=1
```

See also https://redash.io/help/open-source/admin-guide/how-to-upgrade
