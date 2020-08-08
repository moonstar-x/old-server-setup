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

``` bash
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

    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
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

## Setting-up Discord Notifications

One of the cool things about *Tautulli* is that it can send notifications to various types of channels about certain events (an user started streaming something, new media has been added, plex server is down...). With the help of webhooks, we'll set up some notifications for our Discord.

Firstly, on our Discord, we must right click the name of the text channel where we want to set up our notifications. Select `Edit Channel` and head over to `Webhooks`. Here we'll click on `Create Webhook` and modify the Webhook user to fit our needs (name and profile picture). Finally we'll copy the `webhook url` which we'll need in *Tautulli*.

Now we head over to *Tautulli* and go to the `Settings` and then to `Notification Agents`. We'll now `Add a new notification agent` and select `Webhook`.

!!! note
    Keep in mind that there's an option that says `Discord` but it is a bit limited (albeit easier to set up). We'll stick to `Webhook` for a more customized notification message.

Under `configuration`, in `Webhook URL`, paste the URL that you copied previously and keep `Webhook Method` set to `POST`. Enter the `Triggers` tab and select the ones that interest you the most. For this guide, we'll enable `Plex Server Down`, `Plex Server Back Up`, `Plex Remote Access Down`, `Plex Remote Access Back Up` and `Recently Added`.

Now head to the `Data` tab, here you'll need to put the `JSON` body that will be sent to Discord to handle the messages.

!!! tip
    For a detailed guide on how to build your `JSON` bodies to send as Webhooks, check out [this guide](https://birdie0.github.io/discord-webhooks-guide/discord_webhook.html).

Once you've built your `JSON` body, add it to the `JSON Data` text-box and leave the `JSON Headers` empty.

### JSON Body Examples

These notification messages should be customized to your needs. Here's some examples that I currently use.

#### Plex Server Down

* Preview:

![Plex Server Down Preview](https://i.imgur.com/QICK7l8.png)

* `JSON` body:

``` json
{
  "embeds": [
    {
      "color": 15048717,
      "fields": [
        {
          "name": "Plex Status",
          "value": "The Plex Server is currently down! :sob:"
        }
      ],
      "footer": {
         "text": "{server_name} on {server_platform} - {server_version}"
      }
    }  
  ]
}
```

#### Plex Server Back Up

* Preview:

![Plex Server Back Up Preview](https://i.imgur.com/2E1zN27.png)

* `JSON` body:

``` json
{
  "embeds": [
    {
      "color": 15048717,
      "fields": [
        {
          "name": "Plex Status",
          "value": "The Plex Server is back up! :partying_face:"
        }
      ],
      "footer": {
          "text": "{server_name} on {server_platform} - {server_version}"
      }
    }  
  ]
}
```

#### Plex Remote Access Down

* Preview:

![Plex Remote Access Down Preview](https://i.imgur.com/JWiqjJb.png)

* `JSON` body:

``` json
{
  "embeds": [
    {
      "color": 15048717,
      "fields": [
        {
          "name": "Plex Remote Access Status",
          "value": "The Plex Server is currently down! :sob: \n {remote_access_reason}"
        }
      ],
      "footer": {
        "text": "{server_name} on {server_platform} - {server_version}"
      }
    }  
  ]
}
```

#### Plex Remote Access Back Up

* Preview:

![Plex Remote Access Back Up Preview](https://i.imgur.com/679DpSE.png)

* `JSON` body:

``` json
{
  "embeds": [
    {
      "color": 15048717,
      "fields": [
        {
          "name": "Plex Remote Access Status",
          "value": "The Plex Server is back up! :partying_face:"
        }
      ],
      "footer": {
        "text": "{server_name} on {server_platform} - {server_version}"
      }
    }  
  ]
}
```

#### Recently Added

* Preview:

![Recently Added Preview Movie](https://i.imgur.com/l6ilHNC.png)
![Recently Added Preview Music](https://i.imgur.com/4Cem8qA.png)

* `JSON` body:

``` json
{
  "embeds": [
    {
      "title": "Update ({current_day}/{current_month}/{current_year}):",
      <movie>"description": "New movie added!",</movie><show>"description": "New show added!",</show><season>"description": "New season added!",</season><album>"description": "New album added!",</album>
      "color": 15048717,
      "fields": [
        <movie>
        {
          "name": "Movie Name:",
          "value": "{title} ({year})"
        },
        </movie>
        <show>
        {
          "name": "Show Name:",
          "value": "{show_name}"
        },
        </show>
        <season>
        {
          "name": "Show Name:",
          "value": "{show_name}"
        },
        </season>
        <album>
        {
          "name": "Album Name:",
          "value": "{album_name} by {artist_name}"
        },
        </album>
        {
          "name": "Library:",
          "value": ":o: {library_name}",
          "inline": true
        },
        {
          <movie>"name": "Watch Now",</movie><show>"name": "Watch Now",</show><season>"name": "Watch Now",</season><album>"name": "Listen Now",</album>
          "value": "[Plex Web]({plex_url})",
          "inline": true
        }
      ],
      "thumbnail": {
        "url": "{poster_url}"
      },
      "footer": {
        "text": "{server_name} on {server_platform} - {server_version}"
      }
    }  
  ]
}
```

!!! note
    This one requires the following `Conditions`:
    > **Media Type** `is` **movie** `or` **show** `or` **season** `or` **album**
