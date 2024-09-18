# heroku-jupyter

Use this application to deploy [Jupyter Notebook](https://jupyter.org/) to
heroku or CloudFoundry. If a postgres database is available,
[pgcontents](https://github.com/quantopian/pgcontents) is used as notebook
storage.

## Quick start

Jupyter will not start, if the environment variable `JUPYTER_NOTEBOOK_PASSWORD`
was not set.

If you want to customize your app, easiest is to fork this repository.

## Installation instructions

### heroku - automatic deployment

[![Deploy](https://www.herokucdn.com/deploy/button.svg)](https://heroku.com/deploy)

If you forked this repository, you can link it to your heroku app afterwards.

### heroku - manual deployment

Push this repository to your app or fork this repository on github and link your
repository to your heroku app.

To create a new app, run:
```
brew install git

export APP_NAME=jupyter-notebook
export JUPYTER_NOTEBOOK_PASSWORD=sandysheets

export APP_NAME=<your_app_name>
export JUPYTER_NOTEBOOK_PASSWORD=<your_jupyter_password>

git checkout -b main

# Create a new app (or use an existing one you've made)
heroku create $APP_NAME

# Set your required config variable:
heroku config:set JUPYTER_NOTEBOOK_PASSWORD=$JUPYTER_NOTEBOOK_PASSWORD -a $APP_NAME

# Also make the timeout longer:
heroku config:set BUILDPACK_TIMEOUT=1800 -a $APP_NAME

# Specify the buildpack it shuold use (Conda):
heroku buildpacks:set https://github.com/pl31/heroku-buildpack-conda.git -a $APP_NAME


heroku plugins:install heroku-builds --confirm

################################
# OPTIONAL LOCAL RUN:
brew install --cask miniconda
conda init zsh

conda env create --file environment.yml
conda env update --file environment.yml
conda activate heroku-jupyter
conda list notebook

heroku local

################################
# IMPORTANT COMMANDS:
heroku config --app jupyter-notebook

# ssh into build?
heroku run bash --app jupyter-notebook
# or
heroku ps:exec --app jupyter-notebook # ssh into build

git commit -a -m 'update'; git push heroku main

heroku logs --tail -a jupyter-notebook

################################
# TO PURGE THE BUILD CACHE:
heroku plugins:install heroku-builds
heroku builds:cache:purge -a jupyter-notebook

TLDR: the packages used in the original version of this are ancient.

################################
heroku ps:type --app jupyter-notebook

################################
# NO LONGER NEEDED w/ APP.JSON?
# Attach a postgres DB add-on (this will automatically set $DATABASE_URL and $PORT)
heroku addons:create heroku-postgresql:essential-0 -a $APP_NAME

# Make sure at least 1 web dyno is running
heroku ps:scale web=1 -a $APP_NAME

git push heroku main
```


If you are really sure, that you do not want a password protected notebook
server, you can set `JUPYTER_NOTEBOOK_PASSWORD_DISABLED` to `DangerZone!`.



### CloudFoundry
- Clone this repository
- Create a postgres database service with name `jupyter-db`
- Deploy using `cf push`
- Set `JUPYTER_NOTEBOOK_PASSWORD` using `cf set-env`. Do not forget to restart application.

## Environment variables
- `JUPYTER_NOTEBOOK_PASSWORD`: Set password for notebooks
- `JUPYTER_NOTEBOOK_PASSWORD_DISABLED`: Set to `DangerZone!` to disable password protection
- `JUPYTER_NOTEBOOK_ARGS`: Additional command line args passed to `jupyter notebook`; e.g. get a more verbose logging using `--debug`

## Python version

If you want to use a special python version, you should set it in your runtime.txt, for example:
```
python-3.10.6
```

## Environments

*Experimental feature - in work*

- Parametrize default environment using ENVIRONMENT_YML
- Add additional kernel(s) to jupyter installation (Python2 and Python3 in parallel)
- Allow changes and experimental features without damaging defult configuration

| Deployment | Features | Description |
| ---------- | -------- | ----------- |
| [![Deploy](https://www.herokucdn.com/deploy/button.svg)](https://heroku.com/deploy?env[ENVIRONMENT_YML]=environments/default.yml) | Python3, IPython5 | Default Environment
| [![Deploy](https://www.herokucdn.com/deploy/button.svg)](https://heroku.com/deploy?env[ENVIRONMENT_YML]=environments/multi_kernel.yml&env[ADDITIONAL_ENVIRONMENT_YML]=environments/kernel/python2/python2.yml) | Python3, IPython5 + Python2 | Default Environment + Python2
