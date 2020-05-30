# SteamCMD

In this part we'll install **SteamCMD** which is a headless Steam client that lets us install Dedicated server packages without even the need to login (some exceptions apply).

## Installation

Before installing this tool, we'll create a new user, just to better organize ourselves.

``` text
sudo adduser --disabled-login steam
sudo -iu steam
```

Since the *steam* user's home folder is empty, we'll make a folder for SteamCMD.

``` text
mkdir steamcmd && cd steamcmd
```

Now we proceed with the download and extraction.

``` text
wget https://steamcdn-a.akamaihd.net/client/installer/steamcmd_linux.tar.gz
tar zxvf steamcmd_linux.tar.gz && rm steamcmd_linux.tar.gz
```

That's it, you can start up SteamCMD with:

``` text
./steamcmd.sh
```

## Usage

First and foremost, when using SteamCMD you'll need to login, whether it is to your account or to an *anonymous* account (which is basically the same as logging in to an empty user).

``` text
login anonymous
login <username>
```

!!! note
    Use either one, prefer anonymous over your username when possible, keep in mind that some packages require a license for your account, meaning that certain game servers can only be downloaded if you have said game in your Steam library. When inserting your username you'll be prompted to enter your password and your Steam guard code in case you have 2 factor security enabled.

When downloading a package, it is often recommended to save it in a separate folder for better organization.

``` text
force_install_dir <path>
```

To download a package, you'll need it's AppID, a list of Dedicated sever AppIDs can be found [here](https://developer.valvesoftware.com/wiki/Dedicated_Servers_List).

``` text
app_update <app_id> validate
```

## Server Updating

I made a small bash script to help myself update these servers, you can [check it out here](https://github.com/moonstar-x/server-configs/releases/).

### Script Installation

Unzip the content of the file inside the directory where *SteamCMD* is installed. Also, make the script executable with:

``` text
chmod +x update.sh
```

### Script Usage

To run the updater, use:

``` text
./update.sh server_name username
```

If the *username* is empty, the script will run with anonymous as the username. The *server_name* should correspond to a file inside the `./scripts` folder. Keep in mind that the script takes into account that the username in which the server is installed is `steam`, if this is different for you you're free to change the script to suit your needs.
