# Greencoast Variety Discord Bot

## Installing Requirements

The Greencoast Variety Discord Bot required [Node.js](https://nodejs.org), to install it, run the following:

``` text
curl -sL https://deb.nodesource.com/setup_11.x | sudo -E bash -
sudo apt-get install nodejs
```

After executing these two commands, we will have access to the `npm` and `node` commands.

## Installation

To install the Greencoast Variety Bot, we will first clone the project from its repo from the **discord** user:

``` text
sudo -iu discord
git clone https://github.com/greencoast-studios/discord-gcs-variety.git
```

This will create a `discord-gcs-variety` folder which will have everything we need to install our bot.

We can no access the folder and install the dependencies:

``` text
cd discord-gcs-variety
npm install
```

## Configuration

Inside the `config` folder there is a `settings.json.example` file, we'll rename it and edit it:

``` text
mv settings.json.example settings.json
nano settings.json
```

In here, change the settings to your own liking.

!!! tip
    If you need help looking for the voice channel ID, check out [this guide](https://github.com/moonstar-x/discord-downtime-notifier/wiki/Getting-User,-Channel-and-Server-IDs).

After everything is set-up, you can start up the bot with:

``` text
npm start
```

## Auto-starting

Because we want this bot to run on system startup, we'll create a service to make it run when the computer turns on.

Create the service file with:

``` text
sudo nano /etc/systemd/system/discord_gcs_variety.service
```

Insert the following in the editor:

``` bash
[Unit]
Description=Discord Greencoast Studios Bot
After=network.target

[Service]
Type=simple
User=discord
WorkingDirectory=/home/discord/discord-gcs-variety/
ExecStart=/usr/bin/npm start
Restart=always

[Install]
WantedBy=multi-user.target
```

Once saved, start and enable the service.

``` text
sudo systemctl daemon-reload
sudo systemctl start discord_gcs_variety.service
sudo systemctl enable discord_gcs_variety.service
```
