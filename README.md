# Twitter Bot

Twitter Bot

## Getting Up and Running Locally

<div class="index">

pip, virtualenv, PostgreSQL

</div>

### Setting Up Development Environment

Make sure to have the following on your host:

-   Python 3.9
-   [PostgreSQL](https://www.postgresql.org/download/).
-   [Redis](https://redis.io/download), if using Celery

First things first.

1.  Create a virtualenv: :

        $ python3.9 -m venv <virtual env path>

2.  Activate the virtualenv you have just created: :

        $ source <virtual env path>/bin/activate

3.  Install development requirements: :

        $ cd project_root
        $ pip install -r requirements/local.txt
        $ git init # A git repo is required for pre-commit to install
        $ pre-commit install

    <div class="note">

    <div class="title">

    Note

    </div>

    the <span class="title-ref">pre-commit</span> hook exists in the
    generated project as default. For the details of
    <span class="title-ref">pre-commit</span>, follow the
    [pre-commit](https://pre-commit.com/) site.

    </div>

4.  Create a new PostgreSQL database using
    [createdb](https://www.postgresql.org/docs/current/static/app-createdb.html):
    :

        $ createdb --username=postgres <project_slug>

    `project_slug` is what you have entered as the project_slug at the
    setup stage.

    <div class="note">

    <div class="title">

    Note

    </div>

    if this is the first time a database is created on your machine you
    might need an [initial PostgreSQL set
    up](https://web.archive.org/web/20190303010033/http://suite.opengeo.org/docs/latest/dataadmin/pgGettingStarted/firstconnect.html)
    to allow local connections & set a password for the `postgres` user.
    The [postgres
    documentation](https://www.postgresql.org/docs/current/static/auth-pg-hba-conf.html)
    explains the syntax of the config file that you need to change.

    </div>

5.  Set the environment variables for your database(s): :

        $ export DATABASE_URL=postgres://postgres:<password>@127.0.0.1:5432/<DB name given to createdb>
        # Optional: set broker URL if using Celery
        $ export CELERY_BROKER_URL=redis://localhost:6379/0

    <div class="note">

    <div class="title">

    Note

    </div>

    Check out the `settings` page for a comprehensive list of the
    environments variables.

    </div>

    <div class="seealso">

    To help setting up your environment variables, you have a few
    options:

    -   create an `.env` file in the root of your project and define all
        the variables you need in it. Then you just need to have
        `DJANGO_READ_DOT_ENV_FILE=True` in your machine and all the
        variables will be read.
    -   Use a local environment manager like
        [direnv](https://direnv.net/)

    </div>

6.  Apply migrations: :

        $ python manage.py migrate

7.  If you're running synchronously, see the application being served
    through Django development server: :

        $ python manage.py runserver 0.0.0.0:8000

or if you're running asynchronously: :

    $ uvicorn config.asgi:application --host 0.0.0.0 --reload

### Setup Email Backend

#### MailHog

<div class="note">

<div class="title">

Note

</div>

In order for the project to support [MailHog](#mailhog) it must have
been bootstrapped with `use_mailhog` set to `y`.

</div>

MailHog is used to receive emails during development, it is written in
Go and has no external dependencies.

For instance, one of the packages we depend upon, `django-allauth` sends
verification emails to new users signing up as well as to the existing
ones who have not yet verified themselves.

1.  [Download the latest MailHog
    release](https://github.com/mailhog/MailHog) for your OS.

2.  Rename the build to `MailHog`.

3.  Copy the file to the project root.

4.  Make it executable: :

        $ chmod +x MailHog

5.  Spin up another terminal window and start it there: :

        ./MailHog

6.  Check out <http://127.0.0.1:8025/> to see how it goes.

Now you have your own mail server running locally, ready to receive
whatever you send it.

#### Console

<div class="note">

<div class="title">

Note

</div>

If you have generated your project with `use_mailhog` set to `n` this
will be a default setup.

</div>

Alternatively, deliver emails over console via `EMAIL_BACKEND = 'django.core.mail.backends.console.EmailBackend'`.

In production, we have [Mailgun](https://www.mailgun.com/) configured to
have your back\!

### Celery

If the project is configured to use Celery as a task scheduler then by
default tasks are set to run on the main thread when developing locally.
If you have the appropriate setup on your local machine then set the
following in `config/settings/local.py`:

    CELERY_TASK_ALWAYS_EAGER = False

To run Celery locally, make sure redis-server is installed (instructions
are available at <https://redis.io/topics/quickstart>), run the server
in one terminal with <span class="title-ref">redis-server</span>, and
then start celery in another terminal with the following command:

    celery -A config.celery_app worker --loglevel=info

### Sass Compilation & Live Reloading

If you've opted for Gulp as front-end pipeline, the project comes
configured with [Sass](https://sass-lang.com/) compilation and [live
reloading](https://browsersync.io). As you change you Sass/JS source
files, the task runner will automatically rebuild the corresponding CSS
and JS assets and reload them in your browser without refreshing the
page.

1.  Make sure that [Node.js](http://nodejs.org/download/) v16 is
    installed on your machine.

2.  In the project root, install the JS dependencies with:

        $ npm install

3.  Now - with your virtualenv activated - start the application by
    running:

        $ npm run dev

    The app will now run with live reloading enabled, applying front-end
    changes dynamically.

<div class="note">

<div class="title">

Note

</div>

The task will start 2 processes in parallel: the static assets build
loop on one side, and the Django server on the other. You do NOT need to
run Django as your would normally with `manage.py runserver`.

</div>

### Summary

Congratulations, you have made it\! Keep on reading to unleash full potential

### Setting Up Your Users

-   To create a **normal user account**, just go to Sign Up and fill out the form. Once you submit it, you'll see a "Verify Your E-mail Address" page. Go to your console to see a simulated email verification message. Copy the link into your browser. Now the user's email should be verified and ready to go.

-   To create a **superuser account**, use this command:

        $ python manage.py createsuperuser

For convenience, you can keep your normal user logged in on Chrome and your superuser logged in on Firefox (or similar), so that you can see how the site behaves for both kinds of users.

### Type checks

Running type checks with mypy:

    $ mypy twitter_bot

### Test coverage

To run the tests, check your test coverage, and generate an HTML coverage report:

    $ coverage run -m pytest
    $ coverage html
    $ open htmlcov/index.html

#### Running tests with pytest

    $ pytest

### Celery

This app comes with Celery.

To run a celery worker:

```bash
cd twitter_bot
celery -A config.celery_app worker -l info
```

Please note: For Celery's import magic to work, it is important _where_ the celery commands are run. If you are in the same folder with _manage.py_, you should be right.

### Email Server

In development, it is often nice to be able to see emails that are being sent from your application. If you choose to use [MailHog](https://github.com/mailhog/MailHog) when generating the project a local SMTP server with a web interface will be available.

1.  [Download the latest MailHog release](https://github.com/mailhog/MailHog/releases) for your OS.

2.  Rename the build to `MailHog`.

3.  Copy the file to the project root.

4.  Make it executable:

        $ chmod +x MailHog

5.  Spin up another terminal window and start it there:

        ./MailHog

6.  Check out <http://127.0.0.1:8025/> to see how it goes.

Now you have your own mail server running locally, ready to receive whatever you send it.

### Sentry

Sentry is an error logging aggregator service. You can sign up for a free account at <https://sentry.io/signup/> or download and host it yourself.
The system is set up with reasonable defaults, including 404 logging and integration with the WSGI application.

You must set the DSN url in production.

## Deployment

The following details how to deploy this application.

### Heroku

Use the below commands to deploy on heroku

```
heroku create --buildpack heroku/python

heroku addons:create heroku-postgresql:hobby-dev
# On Windows use double quotes for the time zone, e.g.
# heroku pg:backups schedule --at "02:00 America/Los_Angeles" DATABASE_URL
heroku pg:backups schedule --at '02:00 America/Los_Angeles' DATABASE_URL
heroku pg:promote DATABASE_URL

heroku addons:create heroku-redis:hobby-dev

# Assuming you chose Mailgun as mail service (see below for others)
heroku addons:create mailgun:starter

heroku config:set PYTHONHASHSEED=random

heroku config:set WEB_CONCURRENCY=4

heroku config:set DJANGO_DEBUG=False
heroku config:set DJANGO_SETTINGS_MODULE=config.settings.production
heroku config:set DJANGO_SECRET_KEY="$(openssl rand -base64 64)"

# Generating a 32 character-long random string without any of the visually similar characters "IOl01":
heroku config:set DJANGO_ADMIN_URL="$(openssl rand -base64 4096 | tr -dc 'A-HJ-NP-Za-km-z2-9' | head -c 32)/"

# Set this to your Heroku app url, e.g. 'bionic-beaver-28392.herokuapp.com'
heroku config:set DJANGO_ALLOWED_HOSTS=

# Assign with AWS_ACCESS_KEY_ID
heroku config:set DJANGO_AWS_ACCESS_KEY_ID=

# Assign with AWS_SECRET_ACCESS_KEY
heroku config:set DJANGO_AWS_SECRET_ACCESS_KEY=

# Assign with AWS_STORAGE_BUCKET_NAME
heroku config:set DJANGO_AWS_STORAGE_BUCKET_NAME=

git push heroku master

heroku run python manage.py createsuperuser

heroku run python manage.py check --deploy

heroku open
```

#### to setup celery in heroku

Celery requires a few extra environment variables to be ready operational. Also, the worker is created, it’s in the Procfile, but is turned off by default:

_Set the broker URL to Redis_

```
heroku config:set CELERY_BROKER_URL=`heroku config:get REDIS_URL`
```

_Scale dyno to 1 instance_

```
heroku ps:scale worker=1
```
