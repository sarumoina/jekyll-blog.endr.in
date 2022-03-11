---
title: Cheatsheet
layout: post
---



- [Installing the OS](#installing-the-os)
    - [Setting up SSH keys](#setting-up-ssh-keys)
    - [Changing Date Time](#changing-date-time)
- [Installing node js](#installing-node-js)
- [Installing nginx](#installing-nginx)
- [Installing Yarn](#installing-yarn)
- [Installing pm2](#installing-pm2)
- [Cloning repo](#cloning-repo)
- [Environment variables](#environment-variables)
- [Bash script for upload](#bash-script-for-upload)
- [SSL via Let'sEncrypt](#ssl-via-lets-encrypt)
- [Cron](#cron)
    - [Purpose](#purpose)
    - [crontab](#crontab)
    - [cron schedule format](#cron-schedule-format)
    - [Mailing the output of the cron job](#mailing-the-output-of-the-cron-job)
    - [Test the setup](#test-the-setup)
- [Copy files via scp](#copy-files-via-scp)
    - [Creating ssh connection between two servers](#creating-ssh-connection-between-two-servers)
    - [Copy files via SCP](#copy-files-via-scp)
- [Installing PHP](#installing-php)
    - [Install php8.0 fpm](#install-php80-fpm)
    - [Testing the installation](#testing-the-installation)
    - [Installing composer for PHP](#installing-composer-for-php)
    - [Installing Laravel](#installing-laravel)
    - [Troubleshooting while installing PHP](#troubleshooting-while-installing-php)
- [Installing Mariadb](#installing-mariadb)
    - [Creating administrative user](#creating-administrative-user)
- [Installing MongoDB](#installing-mongodb)
- [Installing Symfony](#installing-symfony)



#### Installing the OS

I've chosen Ubuntu 20.04 as the choice for VPS OS.

#### Setting up SSH keys:

You'll require SSH keys in order to prevent logging in to computer with a password everytime.

**In your local computer:**

```console
$ ssh-keygen
# Follow the onscreen directions in order to generate the public and the private pair
$ cat YOUR_KEY.pub
# The public key will appear
# Copy the public key.
```

**In cloudcone web dashboard**

Now go to the profile section in the dashboard of cloudcone and select SSH keys. _Paste the content that you've copied earlier._

Now it is time to login to the server.

Open the terminal and type the following:

```console
ssh root@YOUR_IPV4_IP
```

It will ask for your password which will be provided to you in the registered mail. Enter the password. Now it is time to install the SSH keys.

```console
# STEP 2: INSTALL THE SSH KEY
$ curl -o cc-ikey -L web.cloudc.one/sh/key && sh cc-ikey some-random-key #it will be different for you.
```

Also, if you want to get information about the RAM usage in the system, then execute the following in the terminal.

```console
$ wget -O install stats.cloudcone.sh && bash install some-random-string
# It will be different on every instances. The random string will be provided to you by cloudcone.
```


#### Changing Date Time

To set the timezone, run the following command:

<div class="notification is-info is-light">

**Note**

If you want to get a list of time-zones available, you can use `$ timedatectl list-timezones` You can also use grep in order to narrow it down such as `$ timedatectl list-timezones | grep Asia/Kolkata`
</div>


## Installing Node js

In order to run the full stack server, at first, you will have to install nodejs. At the time of writing this article, nodejs is at version 14.0

```console
# Using Ubuntu
$ curl -sL https://deb.nodesource.com/setup_14.x | sudo -E bash -
$ sudo apt-get install -y nodejs
```
This will install nodejs 14.0 in your server.

<div class="notification is-warning is-light">

**Caution**

if executing sudo apt update returns with an error, then goto the cloudcone control panel > Firewall > Accept. It will update the packages without any hiccups after that

</div>

**Downgrade NodeJS:**

If you want to downgrade to older version then you can do it via the following:

```console
$ npm install -g n   # Install n globally
$ n 14.17.0          # Install and use v0.10.33
```

----

## Installing nginx

To install nginx, run the following command in terminal:

```bash
$ sudo apt install nginx
```


#### Setting up nginx as webserver for nextjs/gatsby

Example config for reverse proxy:

```
# example script for reverse proxy
server {
	server_name example.xopun.com;
	location / {
                # First attempt to serve request as file, then
                # as directory, then fall back to displaying a 404.
                proxy_pass http://localhost:5000;
                proxy_http_version 1.1;
                proxy_set_header Upgrade $http_upgrade;
                proxy_set_header Connection 'upgrade';
                proxy_set_header Host $host;
                proxy_cache_bypass $http_upgrade;
        }
}

```

**warning:** If you get any errors stating Can't start Nginx - Job for nginx.service failed then especially check the line endings. Every line should end with a ; >otherwise this error will popup

#### Setting up nginx as webserver for php

Edit the following in nginx (/etc/nginx/sites-enabled/default):

```
server {

    server_name example.xopun.com;
    listen 80;
    root /var/www/example.xopun.com;

    index index.php;

    location / {
            try_files $uri $uri/ /index.php?$query_string;
    }


    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/run/php/php8.0-fpm.sock;
    }

    location ~ /\.ht {
        deny all;
    }
}
```

We are using the php8.0-fpm as the fastcgi gateway in order to serve the php files.

Restart the nginx server via `sudo systemctl restart nginx`. You can also test the nginx configuration by `sudo nginx -t`.

#### Running multiple server blocks

```
# example script for reverse proxy
# /etc/nginx/sites-enabled/nginx

server {
	server_name example1.xopun.com;
	location / {
                # First attempt to serve request as file, then
                # as directory, then fall back to displaying a 404.
                proxy_pass http://localhost:5000;
                proxy_http_version 1.1;
                proxy_set_header Upgrade $http_upgrade;
                proxy_set_header Connection 'upgrade';
                proxy_set_header Host $host;
                proxy_cache_bypass $http_upgrade;
        }
}

server {
	server_name example2.xopun.com;
	location / {
                # First attempt to serve request as file, then
                # as directory, then fall back to displaying a 404.
                proxy_pass http://localhost:6000;
                proxy_http_version 1.1;
                proxy_set_header Upgrade $http_upgrade;
                proxy_set_header Connection 'upgrade';
                proxy_set_header Host $host;
                proxy_cache_bypass $http_upgrade;
        }
}

```
Restart the server with `sudo systemctl restart nginx` after any modification you do in the configuration.

#### Retreiving errors

    $ sudo tail /var/log/nginx/error.log -n 200

----

## Installing Yarn

To install yarn on the server, execute the following:

```
$ curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add -
$ echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list
$ sudo apt-get update && sudo apt-get install yarn
```

----

## Installing pm2

To install pm2, run the following:

```
$ yarn global add pm2
```

#### Adding to a repo and starting the pm2 process:

In order to run the application in pm2 demon process, do the following for each applications as and when required.

**Note: below codes should be run from the application folder where package.json resides.**

**For nextjs:**

```bash
$ pm2 start yarn --name "nextjs" --interpreter bash -- start
$ pm2 startup # It will enable pm2 to startup automatically when server restarts
$ pm2 show nextjs #to check the status.
```

**For Gatsby**

First, you'll have to install gatsby-cli

    $ npm i -g gatsby-cli

After that, start the pm2 demon process for Gatsby

```bash
$ pm2 start gatsby --name "gatsby" --interpreter bash -- serve
$ pm2 startup # It will enable pm2 to startup automatically when server restarts
$ pm2 show nextjs #to check the status.
```

**Note**

While running the pm2 start nextjs you will get an error because you haven't run the build as of yet. Either run npm build or yarn build > in order to build the production

#### Stopping the pm2 demon process

In order to run the application after pushing the code to the github:

```bash
$ pm2 stop nextjs #[whatever the name of the app]
$ yarn build
$ pm2 start nextjs
```

----

## Cloning repo

Before cloning he repo, make sure that you have generated the SSH key and then uploaded the content from the public key content to the github deploy keys section.

#### Step 1: Generating SSH keys

goto `~/.ssh` folder in your server and generate the key.

```bash
$ ssh-keygen -t rsa
# give the name as id_rsa
# give the passphrase
$ cat id_rsa.pub
```

_Copy the above content to github and paste it in Deploy keys_ . You have to remember that, though the name of the ssh key generated via ssh-keygen can be anything,I have only find it working when the name of the key is id_rsa (There's a workaround though which will be explained later on).

#### Step 2: Testing connections

Sometimes you may get authentication failure error. If such case arises, test the connection first.

    $ ssh -vT git@github.com

**Note**

If you get errors while cloning a public repo, then the most probable cause is, you don't have a id_rsa key. You should upload the key to the >profile/settings/SSH and GPG keys. This should enable you to clone the public repo.

#### Step 3: Specify the particular SSH key for git push

The following is the process via which one can specify the correct ssh key so that while pushing/puling exact ssh key will be used.

Go to the base (project folder where package.json exists) folder and execute the following in the terminal.

```
$ git config core.sshCommand "ssh -i ~/.ssh/id_rsa_example -F /dev/null"
```

----

## Environment variables:

There are two ways you can add environment variables.

#### First way:

```bash
$ sudo nano /etc/environment
```

then, 

    # /etc/environment
    PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games"
    JAVA_HOME=/usr/lib/jvm/adoptopenjdk-11-hotspot-amd64
    LD_LIBRARY_PATH=/usr/local/lib:/usr/lib/postgresql/8.3/lib
    MY_HOME=/home/xopun

```bash
$ source /etc/environment

$ echo $MY_HOME
# Output: /home/xopun
```

#### Second way

```bash
$ sudo vim /etc/environment
```

then,

    # /etc/profile.d/new-env.sh

    export MY_HOME=/home/xopun

```bash
$ source /etc/profile

$ echo $MY_HOME
# Output: /home/xopun
```

## Bash script for upload

You can create a script file in order to make the pulling from the github server easier. The below file will stop the pm2 gatsby app > pull the changes from the github code base > build the gatsby dispatch > restart the pm2 app .

```bash

#!/bin/bash

YELLOW='\033[1;33m'
GREEN='\033[1;32m'
NC='\033[0m' # No Color

clear
echo -e "\n\n${YELLOW}--Adding git files--${NC}\n"
git add .
echo -e "${YELLOW}--Commiting git files--${NC} \n"
git commit -m "automated upload"

echo -e "\n${YELLOW}--Pushing to Git Repository--${NC} \n"
RESULT=`git push 2>&1`

echo -e "${GREEN}Message: ${RESULT} \n\n"

if [ "$RESULT" = "Everything up-to-date" ]
then
    clear
    echo -e "\n\n -- Since everything is up-to-date, no need to connect to the server!--\n"
    echo -e "exiting....\n"
    exit 1
fi

echo -e "\n-------${GREEN}Connecting to the server-------${NC}\n"
ssh user@your_site.com <<EOF

echo -e "\n${GREEN}-------You are right now in cloudcone server and directory will be changed to /srv/gatsby------- ${NC}\n"

cd /srv/gatsby

echo -e "\n${GREEN}-------Stopping the gatsby application pm2 demon process....------- ${NC}\n"

pm2 stop gatsby

echo -e "\n${GREEN}-------pulling the branch from git------- ${NC}\n"

git pull

echo -e "\n\n${GREEN}-------building the gatsby build------- ${NC}\n"

gatsby build

echo -e "\n${GREEN}-------Starting the gatsby application pm2 demon process------- ${NC}\n"
pm2 start gatsby

EOF

echo -e "\n${YELLOW}-------Finished!!!!------- ${NC}\n"

```

By this, you can run the codes automatically with just single script.

you can run this script by typing bash script.sh


## SSL via Let'sEncrypt

#### Installing certbot

Goto https://certbot.eff.org/lets-encrypt/ubuntufocal-nginx (opens new window)for documentation.

```bash
$ sudo snap install --classic certbot #Install the certbot
$ sudo certbot --nginx #Auto Configuration

```
This will enable the SSL version of your site.

#### Redirect all http requests to https

Add the following in `/etc/nginx/sites-enabled/default`

    server {
        listen 80 default_server;

        server_name _;

        return 301 https://$host$request_uri;
    }

#### Reinstalling all the certificates after expiration

If the certificates are due expire, you can extend it via

    certbot

And then follow the onscreen instructions.

## Cron

#### Purpose

cron is a great tool to schedule repetitive tasks. For example, let's assume we have to run the following script which is located at `/home/user/backup/dump.sh`

```bash
#!/bin/bash

$ cd /home/backups
$ mongodump --forceTableScan --uri "mongodb+srv://<username>:<password>@cluster0.a2zef.mongodb.net/<databasename>" --out `date +"%Y-%m-%d"` --gzip

```

*This file is an example of dumping the mongodb data into bson format.*

#### Crontab

Now let's look at how we can make it run after specific period each day so that we don't have to manually take back up.

```bash
$ crontab -e #it will open up the editor choice if open for the first time. Or else, it will open up the file.

$ 0 3 * * * /bin/sh /home/user/backup/dump.sh #exit from nano/vim/other editor

$ sudo cron restart

```

#### cron schedule format

The format of the crontab file is:

MIN HOUR DOM MON DOW CMD

| Short | Description  | Values                      |
| ----- | ------------ | --------------------------- |
| MIN   | Minute field | 0 to 59                     |
| HOUR  | Hour field   | 0 to 23                     |
| DOM   | Day of Month | 1-31                        |
| MON   | Month field  | 1-12                        |
| DOW   | Day Of Week  | 0-6                         |
| CMD   | Command      | Any command to be executed. |

`crontab -l` to list all the cron jobs available for the user.

`crontab -r` will remove all the cron jobs set by that user.

#### Mailing the output of the cron job

In order to send the cron output to via mail, you'll need to setup smtp client.

```bash

sudo apt update
sudo apt install ssmtp mailutils

```
**Configure SMTP**

Edit the config file:

```
sudo nano /etc/ssmtp/ssmtp.conf
```

Next, fill the appropriate information in the conf file

    root=your@gmail.com #your mail id
    mailhub=smtp.gmail.com #mail provider
    FromLineOverride=YES
    hostname=hostname #(you can put anything here)
    AuthUser=your@gmail.com #your mail id
    AuthPass=password #your app password generated from https://myaccount.google.com/apppasswords
    FromLineOverride=YES
    UseSTARTTLS=YES

>**Note**
>
>The password of your gmail account ID won't work while sending mails.
>
>You'll require to generate one in Google app password (opens new window)>and you'll be able to login to the smtp via that app password only.

#### Test the setup

    echo "Here add your email body" | mail -s "Here specify your email subject" your_recepient_email@yourdomain.com

## Copy files via SCP

**Creating ssh connection between two servers and copying files between them**

#### Creating ssh connection between two servers

**Step 1:**

goto `~/.ssh` folder and execute `ssh-keygen`

**Step 2: Copy the public key to the other server**

Execute the following to copy the public key to the remote machine

    ssh-copy-id -i ~/.ssh/mykey user@host

where mykey is the key you generated in step 1.

**Note:**

For the first time, you will have to use password in order to access to the shell in the remote machine.

And done.

#### Copy files via SCP

In order to copy files from server to local,

    $ scp -r user@xexample.com:/folder/backups/2021-01-21/ bak

## Installing PHP

#### Install php8.0 fpm

First install the php8.0 fpm

```bash
$ sudo apt install software-properties-common
$ sudo add-apt-repository ppa:ondrej/php
$ sudo apt update
$ sudo apt install php8.0-fpm

# if you are installing laravel, then install these following too:

$ sudo apt install openssl php-common php-curl php-json php-mbstring php-mysql php-xml php-zip

# if you will use mongodb as driver later on (vide `composer require jenssegers/mongodb`) then install the mongodb drivers prior to that

$ sudo apt install php-dev php-pear
$ sudo pecl install mongodb

# After installing the above, you can install jenssegers/mongodb via composer
```

#### Testing the installation

You can test the installation via the following:

```bash
$ systemctl status php8.0-fpm
```
Now it is time to use php8.0-fpm to serve the php pages.

> **Note:** 
> check [nginx install](#installing-nginx) section in order to know how to setup nginx as webserver for php

#### Installing composer for PHP

To install the composer for php, you'll need to install the dependencies first.

**installing the dependency**

```bash
$ sudo apt update
$ sudo apt install php-cli unzip
```

**Download and install the composer**

```bash
$ cd ~
$ curl -sS https://getcomposer.org/installer -o composer-setup.php
```
Using curl, fetch the latest signature and store it in a variable.

```bash
$ HASH=`curl -sS https://composer.github.io/installer.sig`
```

Following script should be exeuted to check if the script is safe to run.

```bash
$ php -r "if (hash_file('SHA384', 'composer-setup.php') === '$HASH') { echo 'Installer verified'; } 
else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
```
You should see the following:

>Installer verified

The following command will download and install Composer as a system-wide command named composer, under `/usr/local/bin`:

```bash
$ sudo php composer-setup.php --install-dir=/usr/local/bin --filename=composer
```

You should get the following output:

>All settings correct for using Composer Downloading...
>
>Composer (version 1.10.5) successfully installed to: /usr/local/bin/composer Use it: php /usr/local/bin/composer

To test your installation, run the composer:

```bash
$ composer
```

You should get the following:

```

   ______
  / ____/___  ____ ___  ____  ____  ________  _____
 / /   / __ \/ __ `__ \/ __ \/ __ \/ ___/ _ \/ ___/
/ /___/ /_/ / / / / / / /_/ / /_/ (__  )  __/ /
\____/\____/_/ /_/ /_/ .___/\____/____/\___/_/
                    /_/
Composer version 1.10.5 2020-04-10 11:44:22

Usage:
  command [options] [arguments]

Options:
  -h, --help                     Display this help message
  -q, --quiet                    Do not output any message
  -V, --version                  Display this application version
      --ansi                     Force ANSI output
      --no-ansi                  Disable ANSI output
  -n, --no-interaction           Do not ask any interactive question
      --profile                  Display timing and memory usage information
      --no-plugins               Whether to disable plugins.
  -d, --working-dir=WORKING-DIR  If specified, use the given directory as working directory.
      --no-cache                 Prevent use of the cache
  -v|vv|vvv, --verbose           Increase the verbosity of messages: 1 for normal output, 2 for more verbose output and 3 for debug
...

```

#### Installing Laravel

**on local machine**

I prefer to install it via composer so here it goes:

```bash

$ composer create-project laravel/laravel example-app

$ cd example-app

$ php artisan serve
```

Above is for one project only. If you want to install the laravel installer globally, then run the following:

```bash

$ composer global require laravel/installer

$ laravel new example-app

$ php artisan serve

```

This will create the basic structure of the laravel project. if there is no vendor directory, then run composer install in order to fetch the vendor files.

**Now you should create the repo in github so that the local project could be uploaded to it.**

- Create a new repository (DO NOT initiate it with readme or any other files in order to avoid errors later on).
- git clone <URL> and it will clone the repo. (Change the visibility of the project to private if required)
- In your local computer (where the local project resides), go to the terminal and go to the working directory.
- Initialize the local directory as git repository vide the following command

```bash
$ git init -b main
```

Add the files in your repository,

```bash
$ git commit -m "First commit"
# Commits the tracked changes and prepares them to be pushed to a remote repository. 
# To remove this commit and modify the file, use 'git reset --soft HEAD~1' and commit and add the file again.
```

Copy the url of the git repo in github. In terminal, add the following to add the remote repository.

```bash
$ git remote add origin  <REMOTE_URL>
# Sets the new remote
$ git remote -v
# Verifies the new remote URL
```

Push the changes.

```bash
$ git push origin main
# Pushes the changes in your local repository up to the remote repository you specified as the origin
```

This will push the local project in to the github repository.

**Note: -b unknown swtich error**

If you get any errors regarding -b unknown switch, then you should update the git in your machine as -b command is available only after git 2.28 >and later on. In order to update git, do the following:

```bash
$ sudo add-apt-repository -y ppa:git-core/ppa
$ sudo apt update
$ sudo apt install git -y
```

This will update the git in your machine and you will resolve the unknown switch error.

You may get the following error upon pushing repo to github upon second push.


**No upstream branch error**

fatal: The current branch main has no upstream branch. To push the current branch and set the remote as upstream, use

```
git push --set-upstream origin main
```

If you get this error, then run,

```
$ git push --set-upstream origin main
```

**On Server**

Change the visibility of the repo to public so that it can be cloned without much hassle.

git clone &lt;URL&gt; and it will clone the repo. (Change the visibility of the project to private if required)

Setup the ssh deploy keys as written here for future git pull.

run `composer install` in order to complete the setup.

>Required Extensions
>
>There are two specific extensions which are required to be present in the server in order to run composer installer.
>
> - mbstring
> - phpunit

If any errors are thrown above, then you should install these extensions via the following.

```bash
$ sudo apt update
$ sudo apt install php-mbstring phpunit
```
This will install the laravel but the setup is still not finished yet. In order to make life simpler, create a `.env` file in the root directory of the laravel installation.

**Setting up permissions**

```bash
# go to the laravel directory
$ cd /var/www/html/laravel
$ sudo chown -R $USER:www-data .
$ sudo find . -type f -exec chmod 664 {} \;
$ sudo find . -type d -exec chmod 775 {} \;
$ sudo chgrp -R www-data storage bootstrap/cache
$ sudo chmod -R ug+rwx storage bootstrap/cache
```
Above settings should be able to give you the default welcome page of Laravel without any issues.

**Setting up environment**

After setting up the permissions, it is time to setting up the environment

```
$ touch .env
```
In the `.env` file, write the following:

    APP_DEBUG=true
    #I am changing it true in production server as other issues may creep in. Once, everything is ok, change it to false.
    APP_KEY=base64:/<your base64 string>

You should generate the key by `php artisan key:generate` but as I've found that,the system fails to write the config to the env file so write it manually.

## Troubleshooting while installing PHP

if you have more than one php in the same machine, you can switch to different by the following:

```bash
$ sudo update-alternatives --config php
```
This will bring up screen like below:

There are 4 choices for the alternative php (providing /usr/bin/php).

    Selection    Path             Priority   Status
    ------------------------------------------------------------
    * 0            /usr/bin/php7.2   72        auto mode
    1            /usr/bin/php5.6   56        manual mode
    2            /usr/bin/php7.0   70        manual mode
    3            /usr/bin/php7.1   71        manual mode
    4            /usr/bin/php7.2   72        manual mode
    Press <enter> to keep the current choice[*], or type selection number:

>**Extension not found**
>
>If you are getting extension not found/missing errors, then you should install via the following:
>
>   sudo apt install php8.0-gd
>   sudo apt install php8.0-mbstring
>   etc.

## Installing Mariadb

To install MariaDB in ubuntu 20.04, follow the steps:

    sudo apt update
    sudo apt install mariadb-server
    sudo mysql_secure_installation


#### Creating administrative user

    $ sudo mariadb

    MariaDB[(none)]> GRANT ALL ON *.* TO 'admin'@'localhost' IDENTIFIED BY 'password' WITH GRANT OPTION;

## Installing MongoDB

To install Mongodb in ubuntu 20.04, follow the steps:

    wget -qO - https://www.mongodb.org/static/pgp/server-5.0.asc | sudo apt-key add -
    echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/5.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-5.0.list
    sudo apt-get update
    sudo apt-get install -y mongodb-org

To run MongoDB

    sudo systemctl start mongod
    sudo systemctl status mongod
    sudo systemctl enable mongod #mongodb will start after the system start up automatically. 

## Installing Symfony

#### On local machine

Run this installer to create a binary called symfony :

    wget https://get.symfony.com/cli/installer -O - | bash

For traditional application, run

    symfony new --full my_project

For microservice, console application or API:

    symfony new my_project

Then push the repo to git so that the server can pull later on.

#### On server

`git pull` the respository and run `composer install`

#### Permission on the server

These are the permissions required to run Symfony applications:

- The var/log/ directory must exist and must be writable by both your web server user and the terminal user;
- The var/cache/ directory must be writable by the terminal user (the user running cache:warmup or cache:clear commands);
- The var/cache/ directory must be writable by the web server user if you use a filesystem-based cache.

In order to do so, run the following:

    # Go to the symfony root directory
    cd /var/www/symfony
    sudo chown -R $USER:www-data .
    sudo chgrp -R www-data var/log var/cache
    sudo chmod -R ug+rwx var/log var/cache

#### Nginx configuration

    server {

        server_name example.com;
        listen 80;
        root /var/www/symfony/public;

        location / {
            # try to serve file directly, fallback to index.php
            try_files $uri /index.php$is_args$args;
        }

        # optionally disable falling back to PHP script for the asset directories;
        # nginx will return a 404 error when files are not found instead of passing the
        # request to Symfony (improves performance but Symfony's 404 page is not displayed)
        # location /bundles {
        #     try_files $uri =404;
        # }

        location ~ ^/index\.php(/|$) {
            fastcgi_pass unix:/var/run/php/php8.0-fpm.sock;
            fastcgi_split_path_info ^(.+\.php)(/.*)$;
            include fastcgi_params;

            # optionally set the value of the environment variables used in the application
            # fastcgi_param APP_ENV prod;
            # fastcgi_param APP_SECRET <app-secret-id>;
            # fastcgi_param DATABASE_URL "mysql://db_user:db_pass@host:3306/db_name";

            # When you are using symlinks to link the document root to the
            # current version of your application, you should pass the real
            # application path instead of the path to the symlink to PHP
            # FPM.
            # Otherwise, PHP's OPcache may not properly detect changes to
            # your PHP files (see https://github.com/zendtech/ZendOptimizerPlus/issues/126
            # for more information).
            # Caveat: When PHP-FPM is hosted on a different machine from nginx
            #         $realpath_root may not resolve as you expect! In this case try using
            #         $document_root instead.
            fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
            fastcgi_param DOCUMENT_ROOT $realpath_root;
            # Prevents URIs that include the front controller. This will 404:
            # http://domain.tld/index.php/some-path
            # Remove the internal directive to allow URIs like this
            internal;
        }

        # return 404 for all other php files not matching the front controller
        # this prevents access to other php files you don't want to be accessible.
        location ~ \.php$ {
            return 404;
        }

#### Installing DoctrineMongoDBBundle for MongoDB operations

In order to install MongoDB php extension in ubuntu server:

    sudo apt install php-dev php-pear
    sudo pecl install mongodb

then add the extension `extension=mongodb.so` in `php.ini`

> **Note**
>
>In the .env file, set the MONGODB_URL and MONGODB_DB before hand or otherwise composer will fail.

After that, run

    composer require doctrine/mongodb-odm-bundle

and enable the bundle

    # config/bundles.php
    <?php

    return [
        // ...
        Doctrine\Bundle\MongoDBBundle\DoctrineMongoDBBundle::class => ['all' => true],
    ];