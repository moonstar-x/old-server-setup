# 24/7 Music Bot

## Installing Requirements

The 24/7 Music Bot we're going to use requires [Node.js](https://nodejs.org) and ffmpeg, to install them:

```
curl -sL https://deb.nodesource.com/setup_11.x | sudo -E bash -
sudo apt-get install nodejs ffmpeg
```

After executing these two commands, we will have access to the `npm` and `node` commands.

## Installation

To install the 24/7 Music Bot, we will first clone the project from its repo from the **discord** user:

```
sudo -iu discord
git clone https://github.com/moonstar-x/discord-music-24-7.git
```

This will create a `discord-music-24-7` folder which will have everything we need to install our bot.

We can no access the folder and install the dependencies:

```
cd discord-music-24-7
npm install
```

## Configuration

Inside the `config` folder there is a `settings.json.example` file, we'll rename it and edit it:

```
mv settings.json.example settings.json
nano settings.json
```

In here, change the settings to your own liking.

If you need help looking for the voice channel ID, check out [this guide](https://github.com/moonstar-x/discord-downtime-notifier/wiki/Getting-User,-Channel-and-Server-IDs).

After everything is set-up, you can start up the bot with:

```
npm start
```

## Auto-starting

Because we want this bot to run on system startup, we'll create a service to make it run when the computer turns on.

Create the service file with:

```
sudo nano /etc/systemd/system/discord_247_music.service
```

Insert the following in the editor:

    [Unit]
    Description=Discord 24-7 Music Bot
    After=network.target
    
    [Service]
    Type=simple
    User=discord
    WorkingDirectory=/home/discord/discord-music-24-7/
    ExecStart=/usr/bin/npm start
    Restart=always
    
    [Install]
    WantedBy=multi-user.target

Once saved, start and enable the service.

    sudo systemctl daemon-reload
    sudo systemctl start discord_247_music.service
    sudo systemctl enable discord_247_music.service
