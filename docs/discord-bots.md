# Discord Bots
When hosting your own bots for Discord you'll need a bot token, which is generated when creating an application account on Discord. I won't get into further detail, but I'll leave a guide made by jagrosh:

* [Getting a bot token.](https://github.com/jagrosh/MusicBot/wiki/Getting-a-Bot-Token)
* [Finding out your user ID.](https://github.com/jagrosh/MusicBot/wiki/Finding-Your-User-ID)
* [Finding out your user ID.](https://github.com/jagrosh/MusicBot/wiki/Finding-Your-User-ID)
* [Adding your bot to your server.](https://github.com/jagrosh/MusicBot/wiki/Adding-Your-Bot-To-Your-Server)

The following guides are not limited to music bots, you can follow these guides for any kind of bot.

## JDA Music Bot
[jagrosh's Music Bot](https://github.com/jagrosh/MusicBot) made in java with JDA, a Discord API wrapper for java, is one of the best music bots I've seen out there, it does what it says and does it in a simple way - stream music to a Discord voice channel.

To use this bot you'll need Java. To install it run the following:

    sudo apt-add-repository ppa:webupd8team/java
    sudo apt-get update
    sudo apt-get install oracle-java8-installer

Now, to use this bot, we'll create a separate user for Discord, set a password for it and create a folder for our bot.

    sudo adduser --disabled-login discord
    sudo passwd discord
    cd ~
    mkdir JDAMusicBot && cd JDAMusicBot

We'll now download the latest version of [jagrosh's Music Bot](https://github.com/jagrosh/MusicBot/releases).

    wget https://github.com/jagrosh/MusicBot/releases/download/0.1.3/JMusicBot-0.1.3-Linux.jar
    mv JMusicBot-0.1.3-Linux.jar JMusicBot.jar

Once downloaded we'll create the config file.

    nano config.txt

Inside the configuration we'll add the following, make sure to add your own owner ID and bot token, and change any other setting that may best suit your needs.

    token=<your_token_here>
    owner=<your_owner_id>
    prefix=%
    songinstatus=true
    altprefix=!
    stayinchannel=true
    maxtime=0

You can now run your Music Bot.

    java -jar JMusicBot.jar

We'll now create a service so the bot can autostart on login, but before creating it we'll create a simple script that we can use to start the bot instead of running java manually.

    nano start.sh

Add the following text:

    #!/bin/bash
    java -jar JMusicBot.jar

And make the script executable:

    chmod +x start.sh

We can now create the service file as an administrator user:

    sudo nano /lib/systemd/system/discord_jda_musicbot.service

Insert the following in the editor:

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

Once saved, start and enable the service.

    sudo systemctl start discord_jda_musicbot.service
    sudo systemctl enable discord_jda_musicbot.service

> **Note**: If there's an update for the music bot jar file, make sure to rename it to `JMusicBot.jar` or edit the `start.sh` script so it runs the newest version.

## 24/7 Music Bot
[Repulser's Moosic](https://github.com/Repulser/Moosic/) is a pretty good option for our 24/7 music bot that'll serve songs from a text file and stream them on a designated voice channel. Keep in mind that if you have limited bandwidth, you may want to think twice before hosting a 24/7 music bot that'll run even when nobody is in the channel (this should probably be a feature but, oh well...). The music bot is also made using JDA, meaning it's written in Java. Check [JDA Music Bot](#jda-music-bot) for any requirement you may need to fill before running this bot.

We'll create a folder inside our *discord*'s home folder, make sure to run the following as *discord* user that we created above.

    mkdir Moosic && cd Moosic

Now we'll download the bot executable and song list, make sure to get the [latest version](https://github.com/Repulser/Moosic/releases).

    wget https://github.com/Repulser/Moosic/releases/download/5.1/moosic.jar
    wget https://github.com/Repulser/Moosic/releases/download/5.1/songs.txt

We'll need to create our configuration, create the following file:

    nano bot.cfg

And paste the following settings:

    command_prefix=$
    discord_token=<your_bot_token>
    voice_channel_id=<channel_id>
    volume=50

If you need help looking for the voice channel ID, check out [this guide](https://github.com/Chikachi/DiscordIntegration/wiki/How-to-get-a-token-and-channel-ID-for-Discord).

Now we'll create a start script so we can then create a service, just like the previous bot, create the following file:

    nano start.sh

Add the following text:

    #!/bin/bash
    java -jar moosic.jar

And make the script executable:

    chmod +x start.sh

We can now create the service file as an administrator user:

    sudo nano /lib/systemd/system/discord_moosic.service

Insert the following in the editor:

    [Unit]
    Description=Discord 24-7 Music Bot
    After=network.target

    [Service]
    Type=simple
    User=discord
    WorkingDirectory=/home/discord/Moosic
    ExecStart=/home/discord/Moosic/start.sh
    Restart=always

    [Install]
    WantedBy=multi-user.target

Once saved, start and enable the service.

    sudo systemctl start discord_moosic.service
    sudo systemctl enable discord_moosic.service
