Version: 0.1.8
## sigsci-buildpack-heroku

This is the Signal Sciences buildpack for applications deployed on the Heroku platform.

# Dependencies

The `sigsci-start` script now depends on/requires the `wait-for` command (https://github.com/Eficode/wait-for). The `wait-for` script is already included in the `bin/` directory.

# Installation

Follow the steps below to add Signal Sciences to your Heroku application.

1. Login: `heroku login`
1. `cd` to your application folder.
1. Add the Signal Sciences buildpack to your application settings: `heroku buildpacks:add --index 1 https://dl.signalsciences.net/sigsci-heroku-buildpack/sigsci-heroku-buildpack_latest.tgz`
    - Note: The Signal Sciences buildpack must run first, or before your application's primary buildpack.
1. Update your application's `Procfile` by inserting the command `sigsci/bin/sigsci-start`, see the Procfile Examples section below for reference.
1. Add the Signal Sciences keys to your application's environment variables:
  - Note: Keys can be found in Signal Sciences Dashboard under Configurations > Agents and clicking "View Agent Keys".
  - `heroku config:set SIGSCI_ACCESSKEYID=<access key goes here>`
  - `heroku config:set SIGSCI_SECRETACCESSKEY=<secret key goes here>`
1. Deploy the app: `git push heroku master`
1. You should now see the agent in your dashboard. You'll need to trigger a 404 or use one of the Register/Login test links to have the module be detected.

# Special Note

The `sigsci-start` scripts uses the `wait-for` command to prevent the agent from starting before the web process. If the agent process starts before the web process it can cause clients to receive 502s while the dyno is still in the process of starting up. The `wait-for` command helps to resolve that problem. By default the wait-for command will time out after 60 seconds. However, if necessary, Heroku users can specify a custom time out value via the environment variable `SIGSCI_WAITFOR_TIMEOUT`. It may be necessary to increase the time out for web processes that take longer than 60 seconds to start (e.g. Java/Scala).

# Procfile Examples

#### Node.js

`web: sigsci/bin/sigsci-start node index.js`

#### Python

`web: sigsci/bin/sigsci-start gunicorn gettingstarted.wsgi --log-file -`

#### Ruby

`web: sigsci/bin/sigsci-start bundle exec puma -C config/puma.rb`

#### Go

`web: sigsci/bin/sigsci-start go-getting-started`

#### Java

`web: sigsci/bin/sigsci-start java -jar target/helloworld.jar`

#### PHP

`web: sigsci/bin/sigsci-start vendor/bin/heroku-php-apache2 web/`

#### Scala

`web: sigsci/bin/sigsci-start target/universal/stage/bin/play-getting-started -Dhttp.port=${PORT}
console: target/universal/stage/bin/play-getting-started -main scala.tools.nsc.MainGenericRunner -usejavacp`

#### Clojure

`web: sigsci/bin/sigsci-start java $JVM_OPTS -cp target/clojure-getting-started-standalone.jar clojure.main -m clojure-getting-started.web`

# Troubleshooting

- For Node.js, if necessary you may need to add your scale seetings, e.g. `heroku ps:scale web=1`.

- After adding Signal Sciences the application build errors out and does not start.
    - Verify Heroku is properly detecting your application's original buildpack. You can verify this by checking your application's buildpack settings in the Heroku Dashboard.
