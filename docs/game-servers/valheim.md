# Valheim

## Installation

To install a *Valheim* server, change to the `steam` user with:

``` text
sudo -iu steam
```

Then, inside the folder where SteamCMD is installed, run SteamCMD, then login with your account:

``` text
login <username>
```

Once you've entered your password and 2FA code, change the directory where the *Valheim* server will be installed.

``` text
force_install_dir /home/steam/valheim
```

Then, install *Valheim*.

``` text
app_update 896660
```

## Configuration

Inside the folder where *Valheim* is installed, create a file called `start.sh` and add the following:

``` text
export templdpath=$LD_LIBRARY_PATH
export LD_LIBRARY_PATH=./linux64:$LD_LIBRARY_PATH
export SteamAppId=892970

echo "Starting server PRESS CTRL-C to exit"

./valheim_server.x86_64 -name "<Server Name>" -port 2456 -world "world" -password "<Server Password>"

export LD_LIBRARY_PATH=$templdpath
```

!!! note
    Replace `<Server Name>` and `<Server Password>` with your own.

Make the script executable with:

``` text
chmod +x start.sh
```

## Firewall Rules

You will need to allow some ports in UFW, run the following:

``` text
sudo ufw allow 2456:2457/tcp
sudo ufw allow 2456:2457/udp
```

## Running the Server

Lastly, to run the server, just run the start script.

``` text
./start.sh
```
