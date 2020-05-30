# Left 4 Dead 2

## Installation

To install this server, open *SteamCMD*, login anonymously and run the following commands:

    force_install_dir /home/steam/l4d2
    app_update 222860

## Configuration

To configure the server you'll need to create a *server.cfg* file inside the `left4dead2/cfg` folder. You can add the minimum settings:

    hostname "SERVER_NAME"
    rcon_password "SECRET"
    sv_allow_lobby_connect_only 0
    mp_disable_autokick 1
    sv_alltalk 1
    sv_consistency 0
    sv_voiceenable 1
    sv_region 255
    sv_logbans 1
    sv_lan 0
    sv_pure 0
    sv_cheats 0
    sv_steamgroup STEAM_GROUP_ID
    sv_gametypes "coop,survival"
    exec banned_user.cfg
    exec banned_ip.cfg
    writeid
    writeip

    sv_maxcmdrate 0
    sv_maxupdaterate 0
    sv_minrate 25000
    sv_maxrate 50000

## Sourcemod

### Installation

In order to use plugins on your server you'll need *Sourcemod* and *Metamod*, to install these, head over to the [Sourcemod](https://www.sourcemod.net/downloads.php?branch=stable) and [Metamod](https://www.sourcemm.net/downloads.php?branch=stable) download sites and download the latest version inside the mod folder of the game server that you want to install it on (in this case `/home/steam/l4d2/left4dead2`).

    cd /home/steam/l4d2/left4dead2
    wget https://sm.alliedmods.net/smdrop/1.9/sourcemod-1.9.0-git6272-linux.tar.gz
    wget https://mms.alliedmods.net/mmsdrop/1.10/mmsource-1.10.7-git968-linux.tar.gz

Untar the downloaded files and delete them.

    tar -xvf sourcemod-1.9.0-git6272-linux.tar.gz
    tar -xvf mmsource-1.10.7-git968-linux.tar.gz
    rm mmsource-1.10.7-git968-linux.tar.gz sourcemod-1.9.0-git6272-linux.tar.gz

At this point, *Sourcemod* and *Metamod* are both installed, but before we head into the server, we'll set our account as admin. From the same directory, open the following file:

    nano addons/sourcemod/configs/admins_simple.ini

At the bottom, you'll need to append your **STEAMID32 ID**, (which you can find [here](https://steamidfinder.com/)) with the following tag `99:z`, your file should end with a line similar to the following:

    STEAM_0:1:23456789  99:z

### Adding Plugins

TBD.

## Running the Server

To run the server, simply launch *srcds_run* from the *l4d2* folder with the required parameters.

    ./srcds_run -console -game left4dead2 -maxplayers 8 +sv_lan 0 +map c5m1_waterfront
