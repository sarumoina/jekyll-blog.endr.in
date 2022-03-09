## Cheatsheet

- [Installing the OS](#installing-the-os)
    - [Setting up SSH keys](#setting-up-ssh-keys)
    - [Changing Date Time](#changing-date-time)
- [Installing node js](#installing-node-js)
- [Installing nginx](#installing-nginx)
- [Installing Yarn](#installing-yarn)
- [Installing pm2](#installing-pm2)
- [Cloning repo](#cloning-repo)
- [Environment variables](#environment-variables)

<a name='installing-the-os'></a>

#### Installing the OS

I've chosen Ubuntu 20.04 as the choice for VPS OS.

#### Setting up SSH keys:

You'll require SSH keys in order to prevent logging in to computer with a password everytime.

**In your local computer:**

```bash
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

    ssh root@YOUR_IPV4_IP

It will ask for your password which will be provided to you in the registered mail. Enter the password. Now it is time to install the SSH keys.

    # STEP 2: INSTALL THE SSH KEY
    $ curl -o cc-ikey -L web.cloudc.one/sh/key && sh cc-ikey some-random-key #it will be different for you.

Also, if you want to get information about the RAM usage in the system, then execute the following in the terminal.

    $ wget -O install stats.cloudcone.sh && bash install some-random-string
    # It will be different on every instances. The random string will be provided to you by cloudcone.

#### Changing Date Time

To set the timezone, run the following command:

**Note**

If you want to get a list of time-zones available, you can use `$ timedatectl list-timezones` You can also use grep in order to narrow it down such as `$ timedatectl list-timezones | grep Asia/Kolkata`


## Installing Node js

In order to run the full stack server, at first, you will have to install nodejs. At the time of writing this article, nodejs is at version 14.0

```bash
# Using Ubuntu
$ curl -sL https://deb.nodesource.com/setup_14.x | sudo -E bash -
$ sudo apt-get install -y nodejs
```
This will install nodejs 14.0 in your server.

**Caution**

if executing sudo apt update returns with an error, then goto the cloudcone control panel > Firewall > Accept. It will update the packages without any hiccups after that

**Downgrade NodeJS:**

If you want to downgrade to older version then you can do it via the following:

```bash
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


