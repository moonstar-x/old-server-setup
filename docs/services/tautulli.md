# Tautulli

*Tautulli* is a pretty good companion tool for your Plex server that allows you to get information, usage statistics on your Plex libraries and lot of other features.

## Installing Requirements

*Tautulli* requires *Python* to run, for this, we'll install the following dependencies:

``` text
sudo apt-get install git-core python python-setuptools tzdata
```

## Installation

After installing the dependencies, we'll proceed with the installation process which is reduced by the following commands:

``` text
cd /opt
sudo git clone https://github.com/Tautulli/Tautulli.git
```

We'll also create a user that will run *Tautulli*:

``` text
sudo addgroup tautulli && sudo adduser --system --no-create-home tautulli --ingroup tautulli
sudo chown tautulli:tautulli -R /opt/Tautulli
```

## Starting Tautulli

To start *Tautulli*, simply change to the folder where *Tautulli* was installed and start up the *Python* script.

``` text
cd /opt/Tautulli
python Tautulli.py
```

*Tautulli* will be live in the following address: `http://<server_ip>:8181`

## Autostarting Tautulli

To autostart *Tautulli* on system boot, you need to create a service file.

``` text
sudo nano /etc/systemd/system/tautulli.service
```

And in the text editor, paste the following:

``` text
[Unit]
Description=Tautulli - Stats for Plex Media Server usage
Wants=network-online.target
After=network-online.target

[Service]
ExecStart=/opt/Tautulli/Tautulli.py --config /opt/Tautulli/config.ini --datadir /opt/Tautulli --quiet --daemon --nolaunch
GuessMainPID=no
Type=forking
User=tautulli
Group=tautulli
Restart=on-abnormal
RestartSec=5
StartLimitInterval=90
StartLimitBurst=3

[Install]
WantedBy=multi-user.target
```

!!! warning
    Keep in mind that this assumes that the user that will run *Tautulli* is called `tautulli`, if this isn't the case for you, edit the service file accordingly.

Finally we'll enable and start the service.

``` text
sudo systemctl enable tautulli.service
sudo systemctl start tautulli.service
```

## Using a Server Name to Access Tautulli

If you wish to access your *Tautulli* service through a specific domain you will need to use some sort of reverse proxy. From the previous sections where we installed [Gitea](./gitea.md#using-nginx-and-server-names), we created a domain that would be used to access *Gitea*. If you wish to achieve the same for *Tautulli*, please follow the next instructions.

### Installing nginx

If you haven't installed *nginx*, run:

``` text
sudo apt-get install nginx
```

### Configuring Virtual Host

We'll now create a virtual host file:

``` text
sudo nano /etc/nginx/sites-enabled/tautulli
```

And we'll paste the following:

``` text
server {
    listen 80;
    server_name <your-domain>;

    location / {
        proxy_pass http://localhost:8181;
    }

    proxy_set_header X-Real-IP $remote_addr;
}
```

!!! note
    Replace `<your-domain>` with the actual domain that you want to use to access *Tautulli*.

If there's still a *default* configuration, you'll need to remove it:

``` text
sudo rm /etc/nginx/sites-enabled/default
```

And finally, we'll restart *nginx*.

``` text
sudo systemctl restart nginx.service
```

### HTTPS Certificate

If you have `certbot` installed and configured already, you can enable *HTTPS* for *Tautulli* with the following command:

``` text
sudo certbot --nginx
```

If you haven't enabled `certbot` or configured it yet, check out this part of the guide: [Enabling HTTPS for Gitea](./gitea.md#using-certbot).
