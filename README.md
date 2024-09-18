# heroku-jupyter

Use this application to deploy [Jupyter Notebook](https://jupyter.org/) to
heroku or CloudFoundry. If a postgres database is available,
[pgcontents](https://github.com/quantopian/pgcontents) is used as notebook
storage.

## Quick start - Installation instructions

If you want to customize your app, feel free to fork this repository.

The fastest way to get started is to choose option 1. below: automatic deployment on Heroku.

### 1. heroku - automatic deployment (faster & easier)

[![Deploy](https://www.herokucdn.com/deploy/button.svg)](https://heroku.com/deploy)

If you forked this repository, you can link it to your heroku app afterwards.


### 2. heroku - manual deployment

Push this repository to your app or fork this repository on github and link your
repository to your heroku app.

To create a new app, run:
```
export APP_NAME=<your_app_name>
export JUPYTER_NOTEBOOK_PASSWORD=<your_jupyter_password>

# if you don't have git:
brew install git
# clone this repo (swap with your forked repo if you wish):
git clone git@github.com:heroku/heroku-jupyter.git
cd heroku-jupyter

# Create a new app (or use an existing one you've made)
heroku create $APP_NAME

# Set your required config variable:
heroku config:set JUPYTER_NOTEBOOK_PASSWORD=$JUPYTER_NOTEBOOK_PASSWORD -a $APP_NAME

# Specify the buildpack it shuold use (Conda):
heroku buildpacks:set heroku/python -a $APP_NAME
# TODO: Also add in the apt buildpack to make it easier for folks to customize things?

# Make sure at least 1 web dyno is running
heroku ps:scale web=1 -a $APP_NAME

# deploy
git push heroku main
```

Optional useful commands:
```
# show config variables:
heroku config --app $APP_NAME

# ssh into build
heroku run bash --app $APP_NAME

# view logs
heroku logs --tail -a jupyter-notebook
```


If you are really sure that you do not want a password protected notebook (not recommended) and server, you can set `JUPYTER_NOTEBOOK_PASSWORD_DISABLED` to `DangerZone!`:
```
heroku config:set JUPYTER_NOTEBOOK_PASSWORD_DISABLED=DangerZone! -a <your-app-name>
```




### 3. CloudFoundry
- Clone this repository
- Create a postgres database service with name `jupyter-db`
- Deploy using `cf push`
- Set `JUPYTER_NOTEBOOK_PASSWORD` using `cf set-env`. Do not forget to restart application.

## Environment / Config variables
- `JUPYTER_NOTEBOOK_PASSWORD`: Set password for notebooks
- `JUPYTER_NOTEBOOK_PASSWORD_DISABLED`: Set to `DangerZone!` to disable password protection
- `JUPYTER_NOTEBOOK_ARGS`: Additional command line args passed to `jupyter notebook`; e.g. get a more verbose logging using `--debug`


## Python version

If you want to use a special python version, you should set it in your runtime.txt, for example:
```
python-3.10.6
```