# Text-to-Speech Bot

## Installing Requirements

The TTS bot that we're going to use requires [Node.js](https://nodejs.org/) and ffmpeg, for this we will install it:

``` text
curl -sL https://deb.nodesource.com/setup_11.x | sudo -E bash -
sudo apt-get install nodejs ffmpeg
```

After executing these two commands, we will have access to the `npm` and `node` commands.

## Installation

To install the TTS bot, we will first clone the project from its repo from the **discord** user:

``` text
sudo -iu discord
git clone https://github.com/moonstar-x/discord-tts-bot.git
```

This will create a folder called `discord-tts-bot` which will have everything we need to install our bot.

We can now access the folder and install the dependencies:

``` text
cd discord-tts-bot
npm install
```

## Configuration

Inside the `config` folder there is a `settings.json.example` file, we'll rename it and then we'll edit it:

``` text
mv settings.json.example settings.json
nano settings.json
```

In here, change the settings to your own liking.

After everything has been set-up, you can start the bot by inputting:

``` text
npm start
```

## Auto-starting

Just like for the previous bots, we want this one to auto-start on system boot. We'll make a *systemd* service. For this, create the following file using a sudoer user account:

``` text
sudo nano /etc/systemd/system/discord_tts.service
```

Inside the editor, paste the following text:

``` bash
[Unit]
Description=Discord TTS
After=network.target

[Service]
Type=simple
User=discord
WorkingDirectory=/home/discord/discord-tts-bot/
ExecStart=/usr/bin/npm start
Restart=always

[Install]
WantedBy=multi-user.target
```

Now, we refresh, start and enable the service.

``` text
sudo systemctl daemon-reload
sudo systemctl start discord_tts.service
sudo systemctl enable discord_tts.service
```
