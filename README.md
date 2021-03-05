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
* git add, commit, and push all your code to github! 

### Get a Heroku account!

1. Make sure you have an account with heroku: https://www.heroku.com/

2. Make sure you have installed the [Heroku CLI](https://devcenter.heroku.com/articles/heroku-cli)

### Install Heroku CLI

This is a command-line tool that allows us to use commands in the terminal, similar to the way that we use git.

Once it is installed, you need to login with your heroku credentials:

```
heroku login
```

We'll have the ability to create free applications using Heroku, but with limitations. Most of those limitations are related to the size of the databases, as well as uptime for the dynos. For free applications, dynos will "go to sleep" when unused for a period of time. This will lead to slow start times when restarting the dynos.

