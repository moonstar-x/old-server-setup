# SteamCMD

In this part we'll install **SteamCMD** which is a headless Steam client that lets us install Dedicated server packages without even the need to login (some exceptions apply).

## Installation

Before installing this tool, we'll create a new user, just to better organize ourselves.

    sudo adduser --disabled-login steam
    sudo -i -u steam

Since the *steam* user's *home* folder is empty, we'll make a folder for SteamCMD.

    mkdir steamcmd && cd steamcmd

Now we proceed with the download and extraction.

    wget https://steamcdn-a.akamaihd.net/client/installer/steamcmd_linux.tar.gz
    tar zxvf steamcmd_linux.tar.gz && rm steamcmd_linux.tar.gz

That's it, you can start up SteamCMD with:

    ./steamcmd.sh

## Usage

First and foremost, when using SteamCMD you'll need to login, whether it is to your account or to an *anonymous* account (which is basically the same as logging in to an empty user).

    login anonymous
    login <username>

!!! note "Notice:"
    Use either one, prefer anonymous over your username when possible, keep in mind that some packages require a license for your account, meaning that certain game servers can only be downloaded if you have said game in your Steam library. When inserting your username you'll be prompted to enter your password and your Steam guard code in case you have 2 factor security enabled.

When downloading a package it is often recommended to save it in a separate folder for better organization.

    force_install_dir <path>

To download a package, you'll need it's AppID, a list of Dedicated sever AppIDs can be found [here](https://developer.valvesoftware.com/wiki/Dedicated_Servers_List).

    app_update <app_id> validate

## Firewall Rules (Source Games)

For Source Games (cstrike, l4d2, nmrih, etc...) which share the same port range, add the following port range to your firewall rules:

    sudo ufw allow 27000:27050/tcp
    sudo ufw allow 27000:27050/udp

## Server Updating

I made a small bash script to help myself update these servers, you can [check it out here](https://github.com/moonstar-x/server-configs/releases/).

### Script Installation

Unzip the content of the file inside the directory where *SteamCMD* is installed. Also, make the script executable with:

    chmod +x update.sh

### Script Usage

To run the updater, use:

    ./update.sh server_name username

If the *username* is empty, the script will run with anonymous as the username. The *server_name* should correspond to a file inside the `./scripts` folder.