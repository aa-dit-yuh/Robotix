Robotix
==============================

The website for Robotix 2016

.. image:: https://travis-ci.org/Robotix/Robotix.svg?branch=master?style=flat
    :target: https://travis-ci.org/Robotix/Robotix
.. image:: https://landscape.io/github/Robotix/Robotix/master/landscape.svg?style=flat
   :target: https://landscape.io/github/Robotix/Robotix/master
   :alt: Code Health
.. image:: https://coveralls.io/repos/Robotix/Robotix/badge.svg?style=flat
    :target: https://coveralls.io/r/Robotix/Robotix 

*LICENSE*: BSD

Settings
------------

Robotix relies extensively on environment settings which **will not work with Apache/mod_wsgi setups**. It has been deployed successfully with both Gunicorn/Nginx and even uWSGI/Nginx.

For configuration purposes, the following table maps the 'Robotix' environment variables to their Django setting:

======================================= =========================== ============================================== ======================================================================
Environment Variable                    Django Setting              Development Default                            Production Default
======================================= =========================== ============================================== ======================================================================
DJANGO_AWS_ACCESS_KEY_ID                AWS_ACCESS_KEY_ID           n/a                                            raises error
DJANGO_AWS_SECRET_ACCESS_KEY            AWS_SECRET_ACCESS_KEY       n/a                                            raises error
DJANGO_AWS_STORAGE_BUCKET_NAME          AWS_STORAGE_BUCKET_NAME     n/a                                            raises error
DJANGO_CACHES                           CACHES (default)            locmem                                         memcached
DJANGO_DATABASES                        DATABASES (default)         See code                                       See code
DJANGO_DEBUG                            DEBUG                       True                                           False
DJANGO_SECRET_KEY                       SECRET_KEY                  CHANGEME!!!                                    raises error
DJANGO_SECURE_BROWSER_XSS_FILTER        SECURE_BROWSER_XSS_FILTER   n/a                                            True
DJANGO_SECURE_SSL_REDIRECT              SECURE_SSL_REDIRECT         n/a                                            True
DJANGO_SECURE_CONTENT_TYPE_NOSNIFF      SECURE_CONTENT_TYPE_NOSNIFF n/a                                            True
DJANGO_SECURE_FRAME_DENY                SECURE_FRAME_DENY           n/a                                            True
DJANGO_SECURE_HSTS_INCLUDE_SUBDOMAINS   HSTS_INCLUDE_SUBDOMAINS     n/a                                            True
DJANGO_SESSION_COOKIE_HTTPONLY          SESSION_COOKIE_HTTPONLY     n/a                                            True
DJANGO_SESSION_COOKIE_SECURE            SESSION_COOKIE_SECURE       n/a                                            False
DJANGO_EMAIL_BACKEND                    EMAIL_BACKEND               django.core.mail.backends.console.EmailBackend django.core.mail.backends.smtp.EmailBackend
DJANGO_EMAIL_HOST                       EMAIL_HOST                  localhost                                      smtp.sendgrid.com
EMAIL_PORT                              EMAIL_PORT                  1025                                           587
SENDGRID_USERNAME                       EMAIL_HOST_USER             n/a                                            raises error
SENDGRID_PASSWORD                       EMAIL_HOST_PASSWORD         n/a                                            raises error
DJANGO_DEFAULT_FROM_EMAIL               DEFAULT_FROM_EMAIL          n/a                                            "Robotix <noreply@robotix.in>"
EMAIL_SUBJECT_PREFIX                    EMAIL_SUBJECT_PREFIX        n/a                                            "[Robotix] "
======================================= =========================== ============================================== ======================================================================

* TODO: Add vendor-added settings in another table

Getting up and contributing
---------------------------

The steps below will get you up and running with a local development environment. We assume you have the following installed:

* Python 2.7.10
* pip
* virtualenv
* PostgreSQL

1. Fork the `Robotix` repo on GitHub.

2. Clone your fork locally::

    $ git clone git@github.com:your_name_here/Robotix.git

3. Install Linux OS dependencies::

    $ sudo ./install_os_dependencies.sh install

4. Install your local copy into a virtualenv__. This is how you set up your fork for local development::

    $ virtualenv .virtualenv --python=<path_to_python2.7.10>
    $ source ./.virtualenv/bin/activate
    $ ./install_python_dependencies.sh

.. _virtualenv: http://docs.python-guide.org/en/latest/dev/virtualenvs/
Further help: http://stackoverflow.com/questions/5506110/is-it-possible-to-install-another-version-of-python-to-virtualenv

5. Configure the DATABASE_URL environment variable::

    $ sudo -u postgres psql
    postgres=# ALTER USER postgres WITH PASSWORD 'robotix';
    postgres=# CREATE DATABASE robotix;
    postgres=# \q
    $ export DATABASE_URL='postgres://postgres:robotix@localhost:5432/robotix'

6. Run the migrations to create the database schema::

    $ python manage.py migrate

7. You can now run the ``runserver_plus`` command::

    $ python manage.py runserver_plus

The base app will run but you'll need to carry out a few steps to make the sign-up and login forms work. These are currently detailed in `issue #39`_.

.. _issue #39: https://github.com/pydanny/cookiecutter-django/issues/39

8. Create a branch for local development::

    $ git checkout -b name-of-your-bugfix-or-feature

9. When you're done making changes, check that your changes pass flake8, pep8 and the tests::

    $ make lint
    $ make pep8
    $ make test

10. Commit your changes and push your branch to GitHub::

    $ git add .
    $ git commit -m "Your detailed description of your changes."
    $ git push origin name-of-your-bugfix-or-feature

11. Submit a pull request through the GitHub website.

**Live reloading and Sass CSS compilation**

If you'd like to take advantage of live reloading and Sass / Compass CSS compilation you can do so with the included Grunt task.

Make sure that nodejs_ is installed. Then in the project root run::

    $ npm install grunt

.. _nodejs: http://nodejs.org/download/

Now you just need::

    $ grunt serve

The base app will now run as it would with the usual ``manage.py runserver`` but with live reloading and Sass compilation enabled.

To get live reloading to work you'll probably need to install an `appropriate browser extension`_

.. _appropriate browser extension: http://feedback.livereload.com/knowledgebase/articles/86242-how-do-i-install-and-use-the-browser-extensions-

It's time to write the code!!!


Deployment
------------

It is possible to deploy to Heroku or to your own server by using Dokku, an open source Heroku clone.

Heroku
^^^^^^

Run these commands to deploy the project to Heroku:

.. code-block:: bash

    heroku create --buildpack https://github.com/heroku/heroku-buildpack-python

    heroku addons:create heroku-postgresql:dev
    heroku pg:backups schedule DATABASE_URL
    heroku pg:promote DATABASE_URL

    heroku addons:create sendgrid:starter
    heroku addons:create memcachier:dev

    heroku config:set DJANGO_SECRET_KEY=RANDOM_SECRET_KEY_HERE
    heroku config:set DJANGO_SETTINGS_MODULE='config.settings.production'

    heroku config:set DJANGO_AWS_ACCESS_KEY_ID=YOUR_AWS_ID_HERE
    heroku config:set DJANGO_AWS_SECRET_ACCESS_KEY=YOUR_AWS_SECRET_ACCESS_KEY_HERE
    heroku config:set DJANGO_AWS_STORAGE_BUCKET_NAME=YOUR_AWS_S3_BUCKET_NAME_HERE

    heroku config:set SENDGRID_USERNAME=YOUR_SENDGRID_USERNAME
    heroku config:set SENDGRID_PASSWORD=YOUR_SENDGRID_PASSWORD

    git push heroku master
    heroku run python manage.py migrate
    heroku run python manage.py check --deploy
    heroku run python manage.py createsuperuser
    heroku open

Dokku
^^^^^

You need to make sure you have a server running Dokku with at least 1GB of RAM. Backing services are
added just like in Heroku however you must ensure you have the relevant Dokku plugins installed.

.. code-block:: bash

    cd /var/lib/dokku/plugins
    git clone https://github.com/rlaneve/dokku-link.git link
    git clone https://github.com/jezdez/dokku-memcached-plugin memcached
    git clone https://github.com/jezdez/dokku-postgres-plugin postgres
    dokku plugins-install

You can specify the buildpack you wish to use by creating a file name .env containing the following.

.. code-block:: bash

    export BUILDPACK_URL=<repository>

You can then deploy by running the following commands.

..  code-block:: bash

    git remote add dokku dokku@yourservername.com:Robotix
    git push dokku master
    ssh -t dokku@yourservername.com dokku memcached:create Robotix-memcached
    ssh -t dokku@yourservername.com dokku memcached:link Robotix-memcached Robotix
    ssh -t dokku@yourservername.com dokku postgres:create Robotix-postgres
    ssh -t dokku@yourservername.com dokku postgres:link Robotix-postgres Robotix
    ssh -t dokku@yourservername.com dokku config:set Robotix DJANGO_SECRET_KEY=RANDOM_SECRET_KEY_HERE
    ssh -t dokku@yourservername.com dokku config:set Robotix DJANGO_SETTINGS_MODULE='config.settings.production'
    ssh -t dokku@yourservername.com dokku config:set Robotix DJANGO_AWS_ACCESS_KEY_ID=YOUR_AWS_ID_HERE
    ssh -t dokku@yourservername.com dokku config:set Robotix DJANGO_AWS_SECRET_ACCESS_KEY=YOUR_AWS_SECRET_ACCESS_KEY_HERE
    ssh -t dokku@yourservername.com dokku config:set Robotix DJANGO_AWS_STORAGE_BUCKET_NAME=YOUR_AWS_S3_BUCKET_NAME_HERE
    ssh -t dokku@yourservername.com dokku config:set Robotix SENDGRID_USERNAME=YOUR_SENDGRID_USERNAME
    ssh -t dokku@yourservername.com dokku config:set Robotix SENDGRID_PASSWORD=YOUR_SENDGRID_PASSWORD
    ssh -t dokku@yourservername.com dokku run Robotix python manage.py migrate
    ssh -t dokku@yourservername.com dokku run Robotix python manage.py createsuperuser

When deploying via Dokku make sure you backup your database in some fashion as it is NOT done automatically.
