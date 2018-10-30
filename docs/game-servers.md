## SteamCMD
In this part we'll install SteamCMD which is a headless Steam client that lets us install Dedicated server packages without even the need to login (some exceptions apply).
Before installing this tool, we'll create a new user, just to better organize ourselves. We'll also make a password for this user and finally change to that user.

    sudo adduser --disabled-login steam
    sudo passwd steam
    su steam

Since the *steam* user's *home* folder is empty, we'll make a folder for SteamCMD.

    mkdir Steam && cd Steam

Now we proceed with the download and extraction.

    wget https://steamcdn-a.akamaihd.net/client/installer/steamcmd_linux.tar.gz
    tar zxvf steamcmd_linux.tar.gz && rm steamcmd_linux.tar.gz

That's it, you can start up SteamCMD with:

    ./steamcmd.sh

### Usage
First and foremost, when using SteamCMD you'll need to login, whether it is to your account or to an *anonymous* account (which is basically the same as logging in to an empty user).

    login anonymous
    login <username>

> **Notice**: Use either one, prefer anonymous over your username when possible, keep in mind that some packages require a license for your account, meaning that a certain game server can only be downloaded if you have said game in your Steam library. When inserting your username you'll be prompted to enter your password and your Steam guard code in case you have 2 factor security enabled.

When downloading a package it is often recommended to save it in a separate folder for better organization.

    force_install_dir <path>

To download a package, you'll need it's AppID, a list of Dedicated sever AppIDs can be found [here](https://developer.valvesoftware.com/wiki/Dedicated_Servers_List).

    app_update <app_id> validate

For an example of downloading a package, you may want to check the [Arma 3 server installation](#arma-3-server).

## Arma 3 Server
TEXT GOES HERE TEXT GOES HERE TEXT GOES HERE TEXT GOES HERE TEXT GOES HERE TEXT GOES HERE TEXT GOES HERE TEXT GOES HERE

## DayZ Standalone Server
TEXT GOES HERE TEXT GOES HERE TEXT GOES HERE TEXT GOES HERE TEXT GOES HERE TEXT GOES HERE TEXT GOES HERE TEXT GOES HERE

## Assetto Corsa
To install the Assetto Corsa dedicated server we'll need to start SteamCMD using a platform flag to set our platform as *Windows*, basically telling SteamCMD to download the Windows version of the packages we tell it to download.

First we'll change to our *steam* user and go to where our SteamCMD is located.

    su steam
    cd ~/Steam

We'll run SteamCMD with the following argument:

    ./steamcmd.sh +@sSteamCmdForcePlatformType windows

We can now login with our username, we'll be prompted for our password and two factor code if enabled.

    login <username>

We'll change the installation folder:

    force_install_dir ~/Assetto

And now we download the required files.

    app_update 302550

Once we finish downloading we can exit SteamCMD and go to the folder where Assetto Corsa is installed.

    cd ~/Assetto

The configuration of the server is pretty self-explanatory in terms of settings. To change the hostname, the track, the cars, etc.. Edit the following file:

    nano cfg/server_cfg.ini

To change the car list:

    nano cfg/entry_list.ini

You can start up the server with:

    ./acServer

Make sure your ports are forwarded and that the firewall isn't blocking any connections Assetto Corsa may need, the server will not start up if it can't contact Assetto Corsa's main server.

We'll add some firewall rules as a sudoer user (the administrator user for example):

    sudo ufw allow 8081/tcp
    sudo ufw allow 9600/tcp
    sudo ufw allow 9600/udp

> **Notice**: If you wish to run multiple servers or multiple configurations it is advised to reinstall the server in a different location and run a different acServer executable, after all, the server file size isn't too big.

If you want to add mods to the server, you can get them from [here](https://assettocorsa.club/en/), make sure to place them in the correct folder: `./content/cars` or `./content/tracks`, keep in mind the clients will also need the mods installed.

## Minecraft
The following installs a vanilla Minecraft server, if you're looking for a modded server you'll have to look up *Forge* or *CraftBukkit*. Minecraft as we know requires Oracle Java in order to run. Ubuntu does not come with Java installed nor is it available on the default package repos, so we'll need to add a PPA repo and install it from there, just do the following:

    sudo apt-add-repository ppa:webupd8team/java
    sudo apt-get update
    sudo apt-get install oracle-java8-installer

After installing Oracle Java, we're ready to install a Minecraft server. We'll change to our *steam* user since it's dedicated to our game servers. We'll also create a folder specifically for it.

    su Steam
    cd ~
    mkdir Minecraft && cd Minecraft

Head over to the [official Minecraft website](https://minecraft.net/en-us/download/server) to download the newest Minecraft server executable and start it up.

    wget https://launcher.mojang.com/mc/game/1.13/server/d0caafb8438ebd206f99930cfaecfa6c9a13dca0/server.jar
    java -Xms2G -Xmx2G -jar server.jar nogui

> Replace the first two arguments with the amount of RAM you want to dedicate for the Minecraft server.

If everything went well, the server closed itself and a little *eula.txt* file was generated in the server's directory. We need to edit that file to accept the EULA agreement.

    nano eula.txt

Change the last line to:

    eula=true

Now fire up the server again and stop it once it finishes loading up.

    java -Xms2G -Xmx2G -jar server.jar nogui
    stop

Now that all the required files have been generated, we can continue to set up our server's configuration.

    nano server.properties

All the settings here are pretty much self explanatory but there's one that I'm going to address and it's *online-mode*. This setting tells the Minecraft server whether it should connect to the Minecraft.net servers to check if all the users connecting have a Minecraft Premium account and download their respective skins. This option is generally set to *true*, but if we have friends that do not have Minecraft Premium they'll be able to join our server if we set *online-mode=false*, keep in mind that everyone's skins will be set to default and anyone that joins through a 3rd-party launcher may be able to change their name anytime (risking identity theft or ban avoidance).

Remember, we need to allow connections through the firewall (you may need to change to an user that has access to the *sudo* command, i.e you main administrator user):

    sudo ufw allow 25565/tcp

We'll now create a script to run the server a lot easier. Create a file running:

    nano start.sh

Paste the following:

    #!/bin/bash
    java -Xms2G -Xmx2G -jar server.jar nogui

Make it executable:

    chmod +x start.sh

You can now start the server just by running the start script.

    ./start.sh
