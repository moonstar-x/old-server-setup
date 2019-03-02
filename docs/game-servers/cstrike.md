# Counter-Strike 1.6

## Pre-Installation

Before installing this server we will need to create a folder in *steam*'s *Home* directory.

    mkdir ~/.steam/sdk32

Head over to the directory where *SteamCMD* is located and copy **steamclient.so** from the *linux32* folder to the folder we created previously.

    cp ~/Steam/linux32/steamclient.so ~/.steam/sdk32/

## Installation

To install this, start *SteamCMD*, login anonymously:

    login anonymous

Run the following commands:

    force_install_dir /home/steam/cstrike
    app_update 90

## Configuration

## AMX

### Installation

To install plugins in your server you'll need *AMX* and *Metamod* which can be downloaded [here](https://www.amxmodx.org/downloads.php). To install these, download the files inside the *cstrike* mod folder and extract them.

    cd ~/cstrike/cstrike
    wget https://www.amxmodx.org/release/metamod-1.21.1-am.zip
    wget https://www.amxmodx.org/release/amxmodx-1.8.2-base-linux.tar.gz
    wget https://www.amxmodx.org/release/amxmodx-1.8.2-cstrike-linux.tar.gz
    unzip metamod-1.21.1-am.zip
    tar -xvf amxmodx-1.8.2-cstrike-linux.tar.gz
    tar -xvf amxmodx-1.8.2-base-linux.tar.gz
    rm metamod-1.21.1-am.zip amxmodx-1.8.2-base-linux.tar.gz amxmodx-1.8.2-cstrike-linux.tar.gz

Now we have to add the corresponding *dlls* to the *liblist.gam*:

    nano liblist.gam

Inside the editor, replace the following line:

    gamedll_linux "dlls/xxx.so

To:

    gamedll_linux "addons/metamod/dlls/metamod.so"

Close and save the file, we'll need to edit another file now.

    nano addons/metamod/plugins.ini

Inside it, write the following line:

    linux addons/amxmodx/dlls/amxmodx_mm_i386.so

*AMX* is now installed. We need to make ourselves admin, for this edit the following file:

    nano addons/amxmodx/configs/users.ini

And add the following line:

    "STEAM:O:1:23456789" "" "abcdefghijklmnopqrstu" "ce"

Replace `STEAM:0:1:23456789` with your **STEAMID32* which you can find [here](https://steamidfinder.com/).

### Adding Plugins

## Running the Server

To run the server, simply launch *hlds_run* from the *cstrike* folder with the required parameters.

    ./hlds_run -game cstrike +maxplayers 20 +map de_dust2