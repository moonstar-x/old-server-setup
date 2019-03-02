# 24/7 Music Bot

[Repulser's Moosic](https://github.com/Repulser/Moosic/) is a pretty good option for our 24/7 music bot that'll serve songs from a text file and stream them on a designated voice channel. Keep in mind that if you have limited bandwidth, you may want to think twice before hosting a 24/7 music bot that'll run even when nobody is in the channel (this should probably be a feature but, oh well...). The music bot is also made using JDA, meaning it's written in Java. Check [JDA Music Bot](#jda-music-bot) for any requirement you may need to fill before running this bot.

## Installation

We'll create a folder inside our *discord*'s home folder, make sure to run the following as *discord* user that we created above.

    mkdir Moosic && cd Moosic

Now we'll download the bot executable and song list, make sure to get the [latest version](https://github.com/Repulser/Moosic/releases) (again, the example below may be outdated).

    wget https://github.com/Repulser/Moosic/releases/download/5.1/moosic.jar
    wget https://github.com/Repulser/Moosic/releases/download/5.1/songs.txt

## Configuration

We'll need to create our configuration, create the following file:

    nano bot.cfg

And paste the following settings:

    command_prefix=$
    discord_token=<your_bot_token>
    voice_channel_id=<channel_id>
    volume=50

If you need help looking for the voice channel ID, check out [this guide](https://github.com/moonstar-x/discord-downtime-notifier/wiki/Getting-User,-Channel-and-Server-IDs).

## Auto-starting

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