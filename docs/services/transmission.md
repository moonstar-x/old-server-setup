# Transmission

**Transmission** is a torrent downloader that has the option to run in a CLI daemon, perfect for our little headless server.

## Installation

To install **Transmission**, we'll need to add a PPA source, update the sources and directly install.

``` text
sudo add-apt-repository ppa:transmissionbt/ppa && sudo apt-get update
sudo apt-get install transmission-cli transmission-common transmission-daemon
```

## Configuration

Since our server OS is installed in a small SSD, we're going to use an internal HDD to store our downloads. I'll assume you already have the HDD mounted properly. For the sake of the example, we're going to mount our HDD in `/media/internal_sata`.

We're going to create a **Downloads** folder and we'll grant it full permissions so we don't run into issues with permissions when downloading.

``` text
mkdir /media/internal_sata/Downloads
sudo chmod 777 -R /media/internal/sata_Downloads
```

Now to configure it, we need to stop the **Transmission** service and edit the config file.

``` text
sudo systemctl stop transmission-daemon.service
sudo nano /var/lib/transmission-daemon/info/settings.json
```

In this file, we'll edit just the next lines:

``` text
"rpc-enabled": true,
"rpc-password": "password here",
"rpc-username": "user login",
"rpc-whitelist-enabled": false,
"download-dir": "/home/transmission/Downloads",
"incomplete-dir": "/home/transmission/Downloads",
"umask": 2,
```

!!! note
    As a little explanation, *rpc-password* can be set as whatever you want, once the file is saved, your password will be encrypted, so no need to worry about storing your password in plain text. The *rpc-username* can be set as whatever you want, it doesn't have anything to do with your server username, this will be used to login to **Transmission** on a remote session, same goes for *rpc-password*.

We need to add a firewall rule.

``` text
sudo ufw allow 9091/tcp
```

So we can now start the **Transmission** service.

``` text
sudo systemctl start transmission-daemon.service
```

## Connecting to your Transmission Session

There are lots of ways to connect to **Transmission**, my two favorite ones are: [*Transmission Remote GUI*](https://github.com/transmission-remote-gui/transgui/releases) and *Transmission Web GUI*, the latter being the one already included with Transmission. With the first one you can access **Transmission** as if it was a regular Torrent client on your PC, with **Transmission Web**, you can access it through a web browser by accessing `http://serverip:9091`, login with your credentials and you're ready to go.
