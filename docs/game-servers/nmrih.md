# No More Room in Hell

## Installation

To install this server, open *SteamCMD*, login with your account and run the following commands:

    force_install_dir /home/steam/nmrih
    app_update 317670

We'll also need a small dependency, which can be installed with:

    sudo apt-get install lib32tinfo5

Right after the installation completes, change to the directory where *No More Room in Hell* is installed and add the following symlinks.

    cd /home/steam/nmrih/bin
    ln -s soundemittersystem_srv.so soundemittersystem.so
    ln -s scenefilecache_srv.so scenefilecache.so

## Configuration

To configure the server you'll need to create a *server.cfg* file inside the `nmrih/cfg`. You can add the minimum settings:

    hostname "SERVER_NAME"
    rcon_password "SECRET"
    sv_tags "TAGS_HERE"

    sv_lan 0
    sv_cheats 0
    sv_region -1
    sv_voiceenable 1
    sv_alltalk 1

    mapcyclefile "mapcycle.txt"
    sv_allowupload 1
    sv_allowdownload 1
    net_maxfilesize 64

    sv_minrate 0
    sv_maxrate 60000
    sv_minupdaterate 0
    sv_maxupdaterate 66
    sv_timeout 30

    exec banned_user.cfg
    exec banned_ip.cfg
    writeid
    writeip

## Sourcemod

### Installation

In order to use plugins on your server you'll need *Sourcemod* and *Metamod*, to install these, head over to the [Sourcemod](https://www.sourcemod.net/downloads.php?branch=stable) and [Metamod](https://www.sourcemm.net/downloads.php?branch=stable) download sites and download the latest version inside the mod folder of the game server that you want to install it on (in this case `/home/steam/nmrih/nmrih`).

    cd /home/steam/nmrih/nmrih
    wget https://sm.alliedmods.net/smdrop/1.9/sourcemod-1.9.0-git6272-linux.tar.gz
    wget https://mms.alliedmods.net/mmsdrop/1.10/mmsource-1.10.7-git968-linux.tar.gz

Untar the downloaded files and delete them.

    tar -xvf sourcemod-1.9.0-git6272-linux.tar.gz
    tar -xvf mmsource-1.10.7-git968-linux.tar.gz
    rm mmsource-1.10.7-git968-linux.tar.gz sourcemod-1.9.0-git6272-linux.tar.gz

At this point, *Sourcemod* and *Metamod* are both installed, but before we head into the server, we'll set our account as admin. From the same directory, open the following file:

    nano addons/sourcemod/configs/admins_simple.ini

At the bottom, you'll need to append your **STEAMID32**, (which you can find [here](https://steamidfinder.com/)) with the following tag `99:z`, your file should end with a line similar to the following:

    STEAM_0:1:23456789  99:z

### Adding Plugins

## Running the Server

To run the server, simply launch *srcds_run* from the *nmrih* folder with the required folder.

    ./srcds_run -console -game nmrih -maxplayers 8 +sv_lan 0 +map nms_favela