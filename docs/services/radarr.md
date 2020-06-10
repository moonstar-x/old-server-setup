# Radarr

*Radarr* is an **RSS Downloader** focused on movies, it'll help automate movie downloads.

## Requirements

Before installing *Radarr*, we'll need to install *Mono* which is a `C# Runtime` for Linux. Run the following commands to install *Mono*:

``` text
sudo apt-get install gnupg ca-certificates
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 3FA7E0328081BFF6A14DA29AA6A19B38D3D831EF
echo "deb https://download.mono-project.com/repo/ubuntu stable-bionic main" | sudo tee /etc/apt/sources.list.d/mono-official-stable.list
sudo apt-get update
sudo apt-get install mono-devel
```

We also need `mediainfo`, install it with:

``` text
sudo apt-get install mediainfo
```

## Installation

The *Radarr* installation is reduced to the following commands:

``` text
curl -L -O $( curl -s https://api.github.com/repos/Radarr/Radarr/releases | grep linux.tar.gz | grep browser_download_url | head -1 | cut -d \" -f 4 )
tar -xvzf Radarr.develop.*.linux.tar.gz
sudo mv Radarr /opt
```

And finally, we'll create a `radarr` user and group to manage *Radarr*:

``` text
sudo addgroup radarr && sudo adduser --system radarr --ingroup radarr
sudo chown radarr:radarr -R /opt/Radarr
```

## Starting Radarr

To start *Radarr*, run the following command:

``` text
mono --debug /opt/Radarr/Radarr.exe
```

The web service should be available at `http://localhost:7878`.

## Autostarting Radarr

To autostart *Radarr*, we need to create a service file.

``` text
sudo nano /etc/systemd/system/radarr.service
```

And paste the following in the editor:

``` text
[Unit]
Description=Radarr Daemon
After=syslog.target network.target

[Service]
User=radarr
Group=radarr
ExecStart=/usr/bin/mono --debug /opt/Radarr/Radarr.exe -nobrowser
TimeoutStopSec=20
KillMode=process
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

Exit, save and enable and start the service.

``` text
sudo systemctl enable radarr.service
sudo systemctl start radarr.service
```

## Using a Server Name to Access Radarr

If you wish to access *Radarr* through a specific domain, you will need to use some sort of reverse proxy. From the previous sections, where we installed [Gitea](gitea.md), we created a domain that would be used to accessed *Gitea*. In this case, we we'll create a `media` subdomain on our domain to access *Radarr* through `https://media.domain.com/radarr`.

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

    location /radarr {
        proxy_pass http://localhost:8989;
    }

    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
}
```

!!! note
    If you followed the guide for [Sonarr](sonarr.md), just add the `location` directive.

!!! note
    Replace `<your-domain>` with the actual domain that you want to use to access *Radarr*.

If there's still a *default* configuration, you'll need to remove it:

``` text
sudo rm /etc/nginx/sites-enabled/default
```

And finally, we'll restart *nginx*.

``` text
sudo systemctl restart nginx.service
```

After setting-up the reverse proxy, we must also set the base URL for *Radarr* to work properly. Run the following command:

``` text
sudo nano /home/radarr/.config/Radarr/config.xml
```

And inside, look for the line:

``` xml
<UrlBase></UrlBase>
```

And change it to:

``` xml
<UrlBase>/radarr</UrlBase>
```

### HTTPS Certificate

If you have `certbot` installed and configured already, you can enable *HTTPS* for *Radarr* with the following command:

``` text
sudo certbot --nginx
```

If you haven't enabled `certbot` or configured it yet, check out this part of the guide: [Enabling HTTPS for Gitea](./gitea.md#using-certbot).
