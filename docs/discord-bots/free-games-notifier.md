# Free Games Notifier Bot

## Installing Requirements

The free games notifier bot that we're going to use requires [Node.js](https://nodejs.org/), for this we will install it:

``` text
curl -sL https://deb.nodesource.com/setup_11.x | sudo -E bash -
sudo apt-get install nodejs
```

After executing these two commands, we will have access to the `npm` and `node` commands.

## Installation

To install the free games notifier bot, we will first clone the project from its repo from the **discord** user:

``` text
sudo -iu discord
git clone https://github.com/moonstar-x/discord-free-games-notifier.git
```

This will create a folder called `discord-free-games-notifier` which will have everything we need to install our bot.

We can now access the folder and install the dependencies:

``` text
cd discord-free-games-notifier
npm install
```

## Configuration

Inside the `config` folder there is a `settings.json.example` file, we'll rename it and then we'll edit it:

``` text
mv settings.json.example settings.json
nano settings.json
```

In here, change the settings to your own liking.

After everything has been set up, you can start the bot by inputting:

``` text
npm start
```

## Auto-starting

Just like for the previous bots, we want this one to auto-start on system boot. We'll make a *systemd* service. For this, create the following file using a sudoer user account:

``` text
sudo nano /etc/systemd/system/discord_free_games_notifier.service
```

Inside the editor, paste the following text:

``` bash
[Unit]
Description=Discord Free Games Notifier
After=network.target

[Service]
Type=simple
User=discord
WorkingDirectory=/home/discord/discord-free-games-notifier/
ExecStart=/usr/bin/npm start
Restart=always

[Install]
WantedBy=multi-user.target
```

Now, we refresh, start and enable the service.

``` text
sudo systemctl daemon-reload
sudo systemctl start discord_free_games_notifier.service
sudo systemctl enable discord_free_games_notifier.service
```
