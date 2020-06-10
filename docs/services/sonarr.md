# Sonarr

*Sonarr* is an **RSS Downloader** focused on TV series, it'll help automate episode downloads from our favorite shows.

## Requirements

Before installing *Sonarr*, we'll need to install *Mono* which is a `C# Runtime` for Linux. Run the following commands to install *Mono*:

``` text
sudo apt-get install gnupg ca-certificates
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 3FA7E0328081BFF6A14DA29AA6A19B38D3D831EF
echo "deb https://download.mono-project.com/repo/ubuntu stable-bionic main" | sudo tee /etc/apt/sources.list.d/mono-official-stable.list
sudo apt-get update
sudo apt-get install mono-devel
```

## Installation

To install *Sonarr* we need to add its repo first. The installation process is reduced to the following commands:

``` text
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 0xA236C58F409091A18ACA53CBEBFF6B99D9B78493
echo "deb http://apt.sonarr.tv/ master main" | sudo tee /etc/apt/sources.list.d/sonarr.list
sudo apt-get update
sudo apt-get install nzbdrone
```

And finally, we'll create a `sonarr` user and group to manage *Sonarr*:

``` text
sudo addgroup sonarr && sudo adduser --system sonarr --ingroup sonarr
sudo chown sonarr:sonarr -R /opt/NzbDrone
```

## Starting Sonarr

To start *Sonarr*, run the following command:

``` text
mono --debug /opt/NzbDrone/NzbDrone.exe
```

The web service should be available at `http://localhost:8989`.

## Autostarting Sonarr

To autostart *Sonarr*, we need to create a service file.

``` text
sudo nano /etc/systemd/system/sonarr.service
```

And paste the following in the editor:

``` text
[Unit]
Description=Sonarr Daemon
After=network.target

[Service]
User=sonarr
Group=sonarr
ExecStart=/usr/bin/mono --debug /opt/NzbDrone/NzbDrone.exe -nobrowser
TimeoutStopSec=20
KillMode=process
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

Exit, save and enable and start the service.

``` text
sudo systemctl enable sonarr.service
sudo systemctl start sonarr.service
```

## Using a Server Name to Access Sonarr

If you wish to access *Sonarr* through a specific domain, you will need to use some sort of reverse proxy. From the previous sections, where we installed [Gitea](gitea.md), we created a domain that would be used to accessed *Gitea*. In this case, we we'll create a `media` subdomain on our domain to access *Sonarr* through `https://media.domain.com/sonarr`.

### Installing nginx

If you haven't installed *nginx*, run:

``` text
sudo apt-get install nginx
```

### Configuring Virtual Host

We'll now create a virtual host file:

``` text
sudo nano /etc/nginx/sites-enabled/media
```

And we'll paste the following:

``` text
server {
    listen 80;
    server_name <your-domain>;

    location /sonarr {
        proxy_pass http://localhost:8989;
    }

    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
}
```

!!! note
    Replace `<your-domain>` with the actual domain that you want to use to access *Sonarr*.

If there's still a *default* configuration, you'll need to remove it:

``` text
sudo rm /etc/nginx/sites-enabled/default
```

And finally, we'll restart *nginx*.

``` text
sudo systemctl restart nginx.service
```

After setting-up the reverse proxy, me must also set the base URL for *Sonarr* to work properly. Run the following command:

``` text
sudo nano /home/sonarr/.config/NzbDrone/config.xml
```

And inside, look for the line:

``` xml
<UrlBase></UrlBase>
```

And change it to:

``` xml
<UrlBase>/sonarr</UrlBase>
```

### HTTPS Certificate

If you have `certbot` installed and configured already, you can enable *HTTPS* for *Sonarr* with the following command:

``` text
sudo certbot --nginx
```

If you haven't enabled `certbot` or configured it yet, check out this part of the guide: [Enabling HTTPS for Gitea](./gitea.md#using-certbot).
