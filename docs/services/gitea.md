# Gitea (Git Server)

**Gitea** is a very good Git server that lets you host and mirror your Git repositories, its interface is very similar to GitHub's and it's very lightweight.

## Installation

### Initial Setup

For a better organization, we'll create an user for **Gitea**:

``` text
sudo adduser --disabled-login git
```

Since we'll do the initial setup from an external machine, we'll open the port `3000` to access the web interface.

``` text
sudo ufw allow 3000/tcp
```

### Database Creation

Also, **Gitea** will require a database, for this, we'll use **PostgreSQL**. To install it, run:

``` text
sudo apt-get install postgresql
```

This will create a user called `postgres`, we'll need to change to that user and run **PostgreSQL**:

``` text
sudo su postgres
psql
```

Inside here, we'll run the commands to create the database:

``` mysql
CREATE USER gitea WITH PASSWORD '$password';
CREATE DATABASE gitea OWNER gitea;
\q
```

!!! note
    Replace `$password` with the actual password for your database.

### Gitea Install

Change to the `git` user and create a `gitea` folder inside its home directory.

``` text
sudo -iu git
mkdir ~/gitea && cd ~/gitea
```

We can now download the **Gitea** binary (to this date, the latest version is `1.8.1`) and make it executable.

``` text
wget -O gitea https://dl.gitea.io/gitea/1.8.1/gitea-1.8.1-linux-amd64
chmod +x gitea
```

To start **Gitea**, simply run:

``` text
./gitea web
```

## Configuration

To access **Gitea**, open up an Internet browser and go to the IP of the machine that's hosting the service with the port `3000`. You'll be greeted with a configuration webpage which will do the initial configuration for you. After it's done, you should be able to create new users and access the **Gitea** interface.

### Creating a Service

We want **Gitea** to run on system startup, for this, we'll create a *systemd* service:

``` text
sudo nano /etc/systemd/system/gitea.service
```

Paste the following inside the text editor (assuming you named the **Gitea** user `git`).

``` text
[Unit]
Description=Gitea 
After=syslog.target
After=network.target
After=postgresql.service

[Service]
RestartSec=2s
Type=simple
User=git
Group=git
WorkingDirectory=/home/git/gitea
ExecStart=/home/git/gitea/gitea web
Restart=always
Environment=USER=git HOME=/home/git

[Install]
WantedBy=multi-user.target
```

We can now initialize this service with:

``` text
sudo systemd enable gitea.service
sudo systemd start gitea.service
```

## Using nginx and Server Names

We'll use **nginx** to host the service. Since we have other web services running on the server, we can use '*virtual hosts*' to define that we want to access to **Gitea** through `http://git.domain.com` without the need to specify the port.

### Installing nginx

To install **nginx**, run:

``` text
sudo apt-get install nginx
```

### Configuring Virtual Host

To configure the *virtual host*, create a *gitea* configuration file with the following command:

``` text
sudo nano /etc/nginx/sites-enabled/gitea
```

Inside the text editor, paste the following:

``` text
server {
    listen 80;
    server_name <your-domain>;
    client_max_body_size 100M;

    location / {
        proxy_pass http://localhost:3000;
    }

    proxy_set_header X-Real-IP $remote_addr;
}
```

!!! note
    Replace `<your-domain>` with the actual domain that you want to use to access **Gitea**.

We'll now remove the *default* configuration and restart **nginx**:

```
sudo rm /etc/nginx/sites-enabled/default
sudo service nginx reload
```

## Gitea Manual Configuration

While the **Gitea** web configuration is pretty good, we still need to manually change certain things from the configuration file like the domain through which you access **Gitea**. To do this, change to the `git` user and edit the configuration file:

``` text
sudo -iu git
nano ~/gitea/custom/app.ini
```

* Change the `DOMAIN` and `SSH_DOMAIN` directives to the domain that you use to access **Gitea**.
* Change `ROOT_URL` to **https://domain.com**
* Change `DISABLE_SSH` to **true**.

## Changing The Favicon

To change the favicon of the site, create the following folder:

``` text
mkdir -p ~/gitea/custom/public/img
```

In here, paste the image that you want to use as the favicon and rename it to `favicon.png`.

## Using Fail2Ban

**Fail2Ban** is security measure that temporarily IP bans the clients that have failed to login more than 10 times.

### Installing Fail2Ban

To install it, simply run:

``` text
sudo apt-get install fail2ban
```

### Configuring Fail2Ban

We need to create a filter first.

``` text
sudo nano /etc/fail2ban/filter.d/gitea.conf
```

Inside the text editor, paste the following:

``` text
[Definition]
failregex = .*Failed authentication attempt for .* from <HOST>
ignoreregex =
```

We now need to create the jail definition.

``` text
sudo nano /etc/fail2ban/jail.d/jail.local
```

Inside the text editor, paste the following:

``` text
[gitea]
enabled = true
port = http,https
filter = gitea
logpath = /home/git/gitea/log/gitea.log
maxretry = 10
findtime = 3600
bantime = 900
action = iptables-allports
```

Now, restart the **Fail2Ban** service.

``` text
sudo service fail2ban restart
```

## Creating HTTPS Certificates

Since we want to use **HTTPS** to access our **Gitea** service, we'll need some certificates. Luckily, we can use **certbot** to do the following.

### Installing Certbot

To install **certbot**, run the following:

``` text
sudo apt-get install software-properties-common
sudo add-apt-repository ppa:certbot/certbot
sudo apt-get update
sudo apt-get install python-certbot-nginx
```

### Using Certbot

To run **certbot**, use the following command:

``` text
sudo certbot --nginx
```

You'll be prompted with some of your information such as email address, the domain that you need **HTTPS** for and whether you want to enable **HTTP** to **HTTPS** redirection (which I recommend you enable it).

### Setting a Cronjob

Since the certificate only lasts for about 10 days, we'll need to renew periodically. For this, we'll create a *systemd* timer and set it to run everyday.

Create the service file with:

``` text
sudo nano /etc/systemd/system/certbot-renewal.service
```

And paste the following on the text editor:

``` text
[Unit]
Description=Certbot Renewal

[Service]
ExecStart=/usr/bin/certbot renew
```

Now, create the timer file with:

``` text
sudo nano /etc/systemd/system/certbot-renewal.timer
```

And paste the following on the editor:

``` text
[Unit]
Description=Timer for Certbot Renewal

[Timer]
OnBootSec=300
OnUnitActiveSec=1d

[Install]
WantedBy=multi-user.target
```

Enable the timer with:

``` text
sudo systemctl enable certbot-renewal.timer
sudo systemctl start certbot-renewal.timer
```
