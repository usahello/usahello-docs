# API Setup

The Django app can be setup either manually (installing Python, `virtualenv`, Postgres etc.) or using Docker. 

## Prerequisites

Before setting up the project, there's a few things you need to do first.

### Clone the repo

The first step is to clone the repo to you machine: 

```sh
git clone git@bitbucket.org:findhello/find-hello-api.git 
cd find-hello-api
```

### Download Geo database

The API uses [GeoDjango](https://docs.djangoproject.com/en/2.1/ref/contrib/gis/) which is an optional library within the Django ecosystem for dealing with GIS. We need a geo database to use GeoDjango so this step downloads this database.

Visit [this page](https://dev.maxmind.com/geoip/geoip2/geolite2/) and download "GeoLite2 City" (Maxmind DB format) database and unzip it. Create a `geofiles/` directory
in the root of the repo and place the `.mmdb` in there so that the path is `geofiles/GeoLite2-City.mmdb`

### Environment variables

Create a `.env` in the root of the repo with the following:

```sh
DJANGO_SECRET_KEY="secret"
DATABASE_URL=postgresql://find_hello_api:find_hello_api@localhost/find_hello_api
```

---

## Docker setup

Make sure you have [Docker installed on your machine](https://docs.docker.com/install/). On Ubuntu this should
be easy:

```sh
sudo apt-get install docker docker-compose
```

On Windows of Mac, you need to install the Docker desktop pacakges from here:

- https://docs.docker.com/engine/installation/

First build the Docker images (this will take a while):

```sh
docker-compose -f local.yml build
```

then start the Docker containers:

```sh
docker-compose -f local.yml up
```

This should automatically start everything required to work on the site.

Launch the browser at:

```sh
http://localhost:8000
```

Setup a new user and load some fixtures:

```
docker-compose -f local.yml run --rm django python manage.py createsuperuser
docker-compose -f local.yml run --rm django python manage.py loaddata find_hello/location/fixtures/categories.json
docker-compose -f local.yml run --rm django python manage.py loaddata find_hello/location/fixtures/groups.json
```

### Docker issues

#### Resetting the database

If you have any issues with the database that Docker creates, you can reset it:

```sh
docker-compose -f local.yml down
docker volume rm find-hello-api_postgres_backup_local
docker volume rm  find-hello-api_postgres_data_local
docker-compose -f local.yml up --build
docker-compose -f local.yml run --rm django python manage.py createsuperuser
docker-compose -f local.yml run --rm django python manage.py loaddata find_hello/location/fixtures/categories.json
docker-compose -f local.yml run --rm django python manage.py loaddata find_hello/location/fixtures/groups.json
```

---

## Manual setup

Docker can be hard to work with at times, making it difficult to debug issues. An alternative is to run the app natively on your machine instead of with Docker containers.

This assumes that you have Python 3 installed on your machine. The following steps are for MacOS.

### Virtualenv

Install `virtualenv` and `virtualenvwrapper`. These are Python apps that allow you to set up isolated Python environments, a bit like a rudimentary Docker but for Python.

```sh
> pip3 install virtualenv virtualenvwrapper
```

and add the following to the top of your `~/.bashrc` to configure virtualenv:

```
export WORKON_HOME=$HOME/.virtualenvs
source /usr/local/bin/virtualenvwrapper.sh
```

and create a new virtualenv for the API:

```sh
> mkvirtualenv find-hello-api --python=python3
```

To "activate" the virtualenv use the following command:

```sh
> cdvirtualenv find-hello-api
```

and to "deactivate" (so that you revert to using the system Python) use (although don't do this now, as you need to have your new virtualenv activated):

```sh
> deactivate
```

## Postgres

You will need to have an instance of Postgres **9.6** database set up locally on your machine. For MacOS, the easiest option is to use `Postgres.app`. Alternatively you can install Postgres with `brew` (but you need to make sure it's 9.6).

You will also need PostGIS available (which adds GIS support to the Postgresql database). So, if you've installed Postgres via brew, then you will also need:

```sh
> brew install postgis gdal libgeoip
```

If you've installed Postgres.app, it should already have PostGIS included. 

Create a database:

```sh
> psql
```

```sql
> create database find_hello_api;
> create user find_hello_api with password 'find_hello_api';
> grant all privileges on database find_hello_api to find_hello_api;
> \c find_hello_api
> create extension postgis;
> create extension fuzzystrmatch;
> create extension postgis_tiger_geocoder;
```

## Python

First you need to add the repo to the Python path. Add the entire repo to the path using:

```sh
> add2virtualenv /path/to/where/you/git/clones/find-hello-api
```

and reactivate:

```
> workon find-hello-api 
```

Install all requirements:

```sh
> pip install -r requirements/local.txt
```

Set up the database:

```sh
> python manage.py migrate
```

And create a new superuser:

```sh
> python manage.py createsuperuser
```

Load some fixtures:

```sh
> python manage.py loaddata find_hello/location/fixtures/categories.json
> python manage.py loaddata find_hello/location/fixtures/groups.json
```

And run the development server:

```sh
python manage.py runserver
```

You will now be able to see the API via [http://localhost:8000](http://localhost:8000) and the admin dashboard via [http://localhost:8000/admin-dashboard/](http://localhost:8000/admin-dashboard/)
