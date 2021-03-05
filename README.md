# Heroku Deployment with Node + Sequelize


## Before Deploying
* If you don't already have a `.gitignore` file, create it now in the top-level
* Make sure `node_modules` folder is included in `.gitignore`. This ensures that once we delete the `node_modules` folder, git won't find it again.
  * If you've forgotten to use `.gitignore` and accidentally checked in the `node_modules` folder to git, you can remove it with the following command: `git rm -r node_modules`
* Also make sure `.env` is included in `.gitignore`
  * If you've accidentally checked in your `.env` file to git, you can remove it from git with the `git rm .env` command

* Make sure your modules are all installed with `npm i`
* Make sure your code runs with `nodemon`
  * Do you get an error that looks like "xyz module is not found"? This is because originally, we forgot to npm install a module. This might happen for things that you've globally installed when we originally installed it (with the -g flag). Run the following command for each module you're missing:
  `npm install xyz`
* This time we are checking in the `config` folder to github, so remove it from your `.gitignore` - but be sure that you remove any instance where it may have your username and password!
* git `add`, `commit`, and `push` all your code to github! 

### Get a Heroku account and install the CLI tool!

1. Make sure you have an account with heroku: https://www.heroku.com/

2. Make sure you have installed the [Heroku CLI](https://devcenter.heroku.com/articles/heroku-cli)

### Log in

This is a command-line tool that allows us to use commands in the terminal, similar to the way that we use git.

Once it is installed, you need to login with your heroku credentials:

* You may see an option to login via your browser:
![heroku cli](assets/heroku-cli.png)
![heroku website](assets/heroku.png)

* Or you may see an option to log in inside the cli
```
heroku login
Enter your Heroku credentials.
Email: name@example.com
Password (typing will be hidden):
Authentication successful.
```

We'll have the ability to create free applications using Heroku, but with limitations. Most of those limitations are related to the size of the databases, as well as uptime for the dynos. For free applications, dynos will "go to sleep" when unused for a period of time. This will lead to slow start times when restarting the dynos.

# Deploy!
![deploy](assets/snail_deploy.gif)

### To start:

* Create a `Procfile` in the root of your Node application
  * In terminal, run `touch Procfile`. Must be called with a capitol P
  * make sure it is named "Procfile" (no extention)
  * make sure your Procfile is in the same folder as your index.js file
  * in terminal type `echo "web: node server.js" >> Procfile`

* In your `server.js` file, where you get your server started, include the port number in your app.listen function. Example:

```js
app.listen(process.env.PORT || 3000)
```

This ensures that when we set the PORT config variable, Heroku will run on it instead of the 3000 port (Heroku automatically includes a port that's public-facing).

* Your package.json file is **crucial** - when you deploy your application, Heroku will check the package.json file for all dependencies. You can always check your package.json to see if you are missing anything.

* To migrate our live database we need a local version of `sequelize-cli` module in our package json. Add it by running `npm install sequelize-cli`

* Before you create your app in Heroku, be sure your project is being tracked via a git repository.

* Create a Heroku app via the command line (or, if you prefer you can use the GUI to create it and follow its directions to connect it to your git repo)

```
heroku apps:create sitename
```

In this case, `sitename` is the name of your app. This will create a url like: `http://sitename.herokuapp.com` - you'll find that you have to come up with a completely unique name.

* `git add`, `commit` and `push` all your data at this point.

* To push to Heroku, enter the following command

```
git push heroku main
```

This should push all your code to Heroku and trigger a build.

## Heroku Envionment variables

Next we want to make sure we have config variables. This is Heroku's version of a `.env` file or environment variables. Any variable names that are found in your local `.env` file should have a corresponding variable on Heroku.

In your javascript code, you might have something like `process.env.GOOGLE_KEY`.
In order to add environment variables to Heroku. We can run a Heroku command to set it per item in our .env file (see [docs](https://devcenter.heroku.com/articles/config-vars#set-a-config-var) for more on configuring environment vars in heroku)

```
heroku config:set S3_KEY=8N029N81 S3_SECRET=9s83109d3+583493190
```

Alternatively, you can set these fields in the Heroku GUI under the settings tab and then click "Reveal Config Vars".

# Connect a DB with Sequelize
You may notice that while you have a valid URL and the site is deployed, your site likely does not work when you visit it. This is because Heroku is not yet aware of our database and model structure. Let's make it aware. See [Heroku Postgres Docs](https://elements.heroku.com/addons/heroku-postgresql) for more.

* In terminal, install the add-on for postgres: `heroku addons:create heroku-postgresql:hobby-dev`

* Make sure your production variables in `config/config.json` are set like this (pay attention to the production setting).


**config/config.json**
```js
{
  "development": {
    "database": "projectdb",
    "host": "127.0.0.1",
    "dialect": "postgres"
  }
  "production": {
    "use_env_variable": "DATABASE_URL"
  }
}
```
* Add and commit your changes to git, then push your changes to heroku using `git push heroku main`

* So that we don't have to deal with configuring [SSL](https://www.globalsign.com/en/ssl-information-center/what-is-an-ssl-certificate), we need to add one more heroku config variable: `heroku config:set PGSSLMODE=no-verify`

* Now run your migrations by typing in terminal `heroku run sequelize db:migrate` and you should have all your tables set up in the heroku hosted database


* If you need to run any one-off commands, like running seeder files, simply type `heroku run` followed by the command you want from your local terminal

* Try opening your app now, with the command `heroku open` or, simply visit the website in your browser.

# DEBUGGING HELP

## Something is broken?!?!?!?!

Don't panic! Type `heroku logs` to see the error and info messages from the Heroku server! Find the error and Google it if the meaning isn't readily apparent! Some fixes to common errors are found below!

## Overview

* App deployment
  * Login to Heroku using the Heroku CLI (only do once)
  * Create a Heroku app
  * Push application to Heroku
* Sequelize setup
  * Install Heroku's postgres addon
  * Set all config variables on Heroku
  * Install a local version of `sequelize-cli` to the `package.json` file
  * Push application to Heroku
  * Set the production database variable in `config/config.json`
  * Run migrations on Heroku
