# JDA Music Bot

[jagrosh's Music Bot](https://github.com/jagrosh/MusicBot) is made in Java with JDA, a Discord API wrapper for Java. It is one of the best music bots I've seen out there, it does what it says and does it in a simple way - stream music to a Discord voice channel.

## Installing Requirements

To use this bot you'll need Java. To install it run the following:

``` text
sudo apt-add-repository ppa:webupd8team/java
sudo apt-get update
sudo apt-get install oracle-java8-installer
```

## Installation

We'll now download the latest version of [jagrosh's Music Bot](https://github.com/jagrosh/MusicBot/releases) (the example below is most likely outdated).

``` text
sudo -iu discord
mkdir JDAMusicBot && cd JDAMusicBot
wget https://github.com/jagrosh/MusicBot/releases/download/0.2.2/JMusicBot-0.2.2.jar
mv JMusicBot-0.2.2.jar JMusicBot.jar
```

## Configuration

Once downloaded we'll create the config file.

``` text
nano config.txt
```

Inside the configuration we'll add the following, make sure to add your own owner ID and bot token, and change any other setting that may best suit your needs.

``` text
token=<your_token_here>
owner=<your_owner_id>
prefix=%
game="DEFAULT"
status=ONLINE
songinstatus=true
altprefix="NONE"
stayinchannel=true
maxtime=0
updatealerts=false
```

You can now run your Music Bot.

``` text
java -jar JMusicBot.jar
```

## Auto-starting

We'll now create a service so the bot can autostart on login, but before creating it we'll make a simple script that we can use to start the bot instead of running the Java command manually.

``` text
nano start.sh
```

Add the following text:

``` bash
#!/bin/bash
java -jar JMusicBot.jar
```

And make the script executable:

``` text
chmod +x start.sh
```

We can now create the service file as an administrator user:

``` text
sudo nano /lib/systemd/system/discord_jda_musicbot.service
```

Insert the following in the editor:

``` text
[Unit]
Description=Discord JDA Music Bot
After=network.target

[Service]
Type=simple
User=discord
WorkingDirectory=/home/discord/JDAMusicBot
ExecStart=/home/discord/JDAMusicBot/start.sh
Restart=always

[Install]
WantedBy=multi-user.target
```

Once saved, start and enable the service.

``` text
sudo systemctl start discord_jda_musicbot.service
sudo systemctl enable discord_jda_musicbot.service
```

!!! note
    If there's an update for the music bot jar file, make sure to rename it to `JMusicBot.jar` or edit the `start.sh` script so it runs the newest version.
