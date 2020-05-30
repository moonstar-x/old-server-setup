# Deathmatch Classic

## Installation

Before installing, make sure you follow the [Pre-Installation section from Counter-Strike 1.6](./cstrike.md#pre-installation). To install this server, open *SteamCMD*, login anonymously and run the following commands:

``` text
force_install_dir /home/steam/dmc
app_set_config 90 mod dmc
app_update 90 validate
```

## Configuration

TBD.

## AMX

### Installation

To install plugins in your server you'll need *AMX* and *Metamod* which can be downloaded [here](https://www.amxmodx.org/downloads.php). To install these, download the files inside the *dmc* mod folder and extract them.

``` text
cd ~/dmc/dmc
wget https://www.amxmodx.org/release/metamod-1.21.1-am.zip
wget https://www.amxmodx.org/release/amxmodx-1.8.2-base-linux.tar.gz
unzip metamod-1.21.1-am.zip
tar -xvf amxmodx-1.8.2-base-linux.tar.gz
rm metamod-1.21.1-am.zip amxmodx-1.8.2-base-linux.tar.gz
```

Now we have to add the corresponding *dlls* to the *liblist.gam*:

``` text
nano liblist.gam
```

Inside the editor, replace the following line:

``` text
gamedll_linux "dlls/xxx.so
```

To:

``` text
gamedll_linux "addons/metamod/dlls/metamod.so"
```

Close and save the file, we'll need to edit another file now.

``` text
nano addons/metamod/plugins.ini
```

Inside it, write the following line:

``` text
linux addons/amxmodx/dlls/amxmodx_mm_i386.so
```

*AMX* is now installed. We need to make ourselves admin, for this edit the following file:

``` text
nano addons/amxmodx/configs/users.ini
```

And add the following line:

``` text
"STEAM:O:1:23456789" "" "abcdefghijklmnopqrstu" "ce"
```

Replace `STEAM:0:1:23456789` with your **STEAMID32* which you can find [here](https://steamidfinder.com/).

### Adding Plugins

TBD.

## Running the Server

To run the server, simply launch *hlds_run* from the *dmc* folder with the required parameters.

``` text
./hlds_run -game dmc +maxplayers 10 +map dmc_dm2
```
