# Services
## TeamSpeak 3
With the existence of Discord, it seems like **TeamSpeak 3** is losing it's popularity but for the sake of the server's utility, we'll still make a **TeamSpeak 3** server.

### Installation
Firstly we need to make a new user.

    sudo adduser --disabled-login teamspeak
    sudo -i -u teamspeak

We can now download the [newest version of TeamSpeak 3 Server](https://www.teamspeak.com/en/downloads#server) and we extract it. *(Again, the link on the example below may be outdated and/or unavailable.)*

    mkdir Downloads && cd Downloads
    wget http://dl.4players.de/ts/releases/3.3.0/teamspeak3-server_linux_amd64-3.3.0.tar.bz2
    tar xvf teamspeak3-server_linux_amd64-3.3.0.tar.bz2

Before starting the server we need to accept **TeamSpeak 3**'s license agreement.

    touch .ts3server_license_accepted

### Auto-starting
Now, we need to make a *systemctl* service so the server can run as a service and autostart with the server, this should be done in a sudoer user account. Create the daemon with:

    sudo nano /lib/systemd/system/teamspeak.service

And in the text editor, paste the following and save.

    [Unit]
    Description=TeamSpeak 3 Server
    After=network.target

    [Service]
    WorkingDirectory=/home/teamspeak/
    User=teamspeak
    Group=teamspeak
    Type=forking
    ExecStart=/home/teamspeak/ts3server_startscript.sh start inifile=ts3server.ini
    ExecStop=/home/teamspeak/ts3server_startscript.sh stop
    PIDFile=/home/teamspeak/ts3server.pid
    RestartSec=15
    Restart=always

    [Install]
    WantedBy=multi-user.target

We can now reload *systemctl* and start the service.

    sudo systemctl --system daemon-reload
    sudo systemctl enable teamspeak.service
    sudo systemctl start teamspeak.service

The server is now up and running but before we continue, we need to set up some things.

### Configuration
We can get our server's privilege key so we can turn ourselves as the server admin.

    cat /home/teamspeak/logs/ts3server_*

You'll receive an output similar to this one:

    --------------------------------------------------------
    ServerAdmin privilege key created, please use the line below
    token=****************************************
    --------------------------------------------------------

Lastly, let's not forget to add firewall rules to accept **TeamSpeak 3** connections.

    sudo ufw allow 9987/udp
    sudo ufw allow 30033/tcp
    sudo ufw allow 10011/tcp

Finally, once you connect to your server, you'll be prompted to add the privilege key, enter it to become server admin and change any server configuration from within your client.

## NO-IP DUC
**NO-IP DUC** is a client that updates the public IP of your server to a NO-IP host. This is particularly useful if you want to have a free hostname instead of having to use your IP directly or if your public IP is not static (which is the case for most people). For this you obviously need a NO-IP account, you can create one [here](https://www.noip.com/sign-up).

### Installation
To install this just follow this chain of commands:

    cd /usr/local/src/
    sudo wget http://www.no-ip.com/client/linux/noip-duc-linux.tar.gz
    sudo tar xf noip-duc-linux.tar.gz
    cd noip-2.1.9-1/
    sudo make install
    cd .. && sudo rm -rf noip*

This last command will ask for your login and password for the NO-IP site, it'll then ask you which of your hosts you want to update and in which interval.

### Auto-starting
We finally need to create a *systemctl* service so it can autostart on system boot. We make the service file with:

    sudo nano /etc/systemd/system/noip2.service

Add the following text to the editor and save the file.

    [Unit]
    Description=No-IP Dynamic DNS Update Client
    After=network.target

    [Service]
    Type=forking
    ExecStart=/usr/local/bin/noip2

    [Install]
    WantedBy=multi-user.target
    ```
    Lastly, to enable the service:
    ```
    systemctl daemon-reload
    systemctl enable noip2.service
    systemctl start noip2.service

## Squid (HTTP Proxy)
**Squid** is an easy to set up HTTP proxy with the ability to do web-caching, meaning that if multiple users connect to the proxy and then try to download the same file, the process will be sped up for the second and next clients downloading because the file will be stored in the server's cache. An HTTP proxy is useful when you need to access IP whitelisted applications from other locations without the need to use a VPN.

### Installation
Anyways, to install **Squid**:

    sudo apt-get install squid apache2-utils

We'll install **apache2-utils** to generate users so we can limit the proxy connections by user authentication.

### Configuration
To set up said **Squid** users:

    sudo touch /etc/squid/squid_passwd
    sudo chown proxy /etc/squid/squid_passwd
    sudo htpasswd /etc/squid/squid_passwd $USER

> Change **$USER** with the username you want to create, you'll be then asked to enter a password. Keep in mind you can create as many users as you need.

We can now edit **Squid**'s configuration:

    sudo nano /etc/squid/squid.conf

First we'll change the port that **Squid** will listen to (we like 1337/TCP for this). Look for and replace the following:

    http_port 1337

Now we'll tell **Squid** to add the user authentication parameters we need. Find the following, uncomment these parameters and edit them if needed.

    auth_param basic program /usr/lib/squid/basic_ncsa_auth /etc/squid/squid_passwd
    acl ncsa_users proxy_auth REQUIRED
    auth_param basic real Squid proxy-caching web server
    auth_param basic credentialsttl 12 hours

Before finishing, we'll tell **Squid** to allow connections for authenticated users. Look for `http_access deny all` and above that line add the following:

    http_access allow ncsa_users

Now we'll create a new firewall rule to allow connections to the *1337/TCP* port.

    sudo ufw allow 1337/tcp

We can now restart **Squid**.

    sudo service squid restart

We're now ready to use our HTTP proxy, in your internet browser you can add your IP and the port you used to the proxy settings, if you try to connect with no authentication you'll get an error when browsing, you'll need to login with your user.

## Plex
**Plex** is an amazing tool that lets you run a media server on your computer. You can add your own music, movies, TV series or even pictures and stream them from an Internet browser or a mobile device (with the Plex app). This is pretty useful for all of us who keep a collection of movies or TV series DVD's and want them in a single place. The best feature this program has is the ability to share your library with your friends.

### Installation
Unlike the other programs, this one installs in a pretty easy way. First head over to the [Plex downloads site](https://www.plex.tv/media-server-downloads/#plex-media-server) and download the latest release for your operating system. *(Keep in mind the link in the example may be outdated.)*

    wget https://downloads.plex.tv/plex-media-server/1.13.5.5291-6fa5e50a8/plexmediaserver_1.13.5.5291-6fa5e50a8_amd64.deb

Now we proceed with the installation:

    sudo dpkg -i plexmediaserver_1.13.5.5291-6fa5e50a8_amd64.deb
    sudo systemctl enable plexmediaserver.service
    sudo systemctl start plexmediaserver.service

Just like the other programs, we need to add a firewall rule.

    sudo ufw allow 32400/tcp

We can now access the web interface via `http://serverip:32400/web` to set up the server and link it to our Plex account.

### Adding Multiple Hard Drives
Maybe your media collection is so big that you cannot fit it in one single drive, you insert a second hard drive to your computer, you plug in your USB hard drive only to find out that when trying to add it to your Plex library you get bombarded with errors. This issue happens when there's some problems with the permissions system and the *plex* user.

In order to fix this issue we'll need to change some settings with the *plex* user, enter the following commands (replace **$USER** with your actual username):

    sudo gpasswd -a plex plugdev
    sudo gpasswd -a plex root
    sudo gpasswd -a plex sudo
    sudo gpasswd -a plex $USER
    sudo gpasswd -a $USER plex

Now we'll add our hard drives to the mount points that we'll create so they get mounted and recognized by **Plex** on startup. In our case we'll add an internal (SATA) drive and an external (USB) drive.

    mkdir ~/plex_sata ~/plex_usb

I'll assume your drives are already partitioned and formatted. We can list the drives with:

    sudo blkid

You'll receive an output similar to this one:

    /dev/sda2: UUID="f5a613fe-981a-11e8-937c-080027498561" TYPE="ext4" PARTUUID="544fba45-03a8-4b4f-a199-26f6b1b1d94b"
    /dev/loop0: TYPE="squashfs"
    /dev/sda1: PARTUUID="46e94feb-2ed3-4b1f-b21f-41d452fe2b13"

> Notice here that there's only one hard drive plugged in so it only recognizes /dev/sda, this is called the ID. If you have a drive plugged in and it does not show up in this list, it is likely not partitioned or formatted. Do not proceed if this happens. Instead use **fstab** to fix your issues and then come back.

Identify the drive ID that corresponds with what you need to add to **Plex**, copy the UUID that is displayed, we'll need it later. We need to edit our file system table.

 **PROCEED AT YOUR OWN RISK, MESSING UP THIS FILE WILL MOST PROBABLY BREAK YOUR COMPUTER!**

 Access the *fstab* file:

    sudo nano /etc/fstab

We now need to add the following in a new line (separate everything with a `Tab`).

    UUID=$UUID  $DIR	$FORMAT	0	0

If you replace everything with your information you should get something like this.

    UUID=A82CC6B45D20  /home/server/plex_usb	ntfs	0	0

Finally, reboot your server. You can now add your external hard drive in the **Plex** library by adding the folder you added as a mount point.

## Samba (SMB/CIFS)
**Samba** lets your Linux based server share files and folders on a Windows File Sharing Workgroup using the same protocol (SMB/CIFS), this is pretty useful when you need to share files between computers on your network and can even be accessed through the Internet (although it should only be done through a VPN for security purposes).

### Installation
To install **Samba**:

    sudo apt-get install samba

### Configuring
We'll make a folder on our *home* directory, this will serve as the share folder which will be accessible through *SMB*.

    cd ~ && mkdir sambashare

We now need to add this folder entry to the configuration. Open up **Samba**'s config file.

    sudo nano /etc/samba/smb.conf

Now we'll add the folder we created alongside other directories to the **Samba** share (note that you should replace these directories corresponding to your needs). You can add as many entries as you need as long as they're well formatted.

    [sambashare]
        comment = Samba shared folder
        path = /home/$USER/sambashare
        read only = no
        browsable = yes

    [home-users]
        comment = Home users folder
        path = /home
        read only = no
        browsable = yes

Now we restart the **Samba** service to apply the changes:

    sudo service smbd restart

Before trying to access the share with another computer, we'll need to add a password to the **Samba** user. Use the next command and replace **$USER** with your username.

    sudo smbpasswd -a $USER

Now, as a final step, add the firewall rule to allow *SMB* connections.

    sudo ufw allow 445/tcp

## Transmission
**Transmission** is a torrent downloader that has the option to run in a CLI daemon, perfect for our little headless server.

### Setting-up User
In order to install this we need to make an user to better organize our storage and set up a password so we can log in as this new user.

    sudo adduser --disabled-login transmission
    sudo -i -u transmission

Since the new user does not have anything in its *home* folder, we'll make the Downloads folder and quickly switch back to our administrator user.

    mkdir Downloads && exit

### Installation
Now, to download **Transmission**, we'll need to add a PPA source, update the sources and directly install.

    sudo add-apt-repository ppa:transmissionbt/ppa && sudo apt-get update
    sudo apt-get install transmission-cli transmission-common transmission-daemon

### Configuration
Now to configure it we need to stop the **Transmission** service and edit the config file.

    sudo systemctl stop transmission-daemon.service
    sudo nano /var/lib/transmission-daemon/info/settings.json

In this file, we'll edit just the next lines:

    "rpc-enabled": true,
    "rpc-password": "password here",
    "rpc-username": "user login",
    "rpc-whitelist-enabled": false,
    "download-dir": "/home/transmission/Downloads",
    "incomplete-dir": "/home/transmission/Downloads",
    "umask": 2,

> As a little explanation, *rpc-password* can be set as whatever you want, once the file saves your password will be encrypted, so no need to worry about storing your password in plain text. The *rpc-username* can be set as whatever you want, it doesn't have anything to do with your server username, this will be used to login to **Transmission** on a remote session, same goes for *rpc-password*.

We need to add a firewall rule.

    sudo ufw allow 9091/tcp

So we can now start the **Transmission** service.

    sudo systemctl start transmission-daemon.service

### Connecting to your Transmission Session
There are lots of ways to connect to **Transmission**, my two favorite ones are: [*Transmission Remote GUI*](https://github.com/transmission-remote-gui/transgui/releases) and *Transmission Web GUI*, the latter being the one already included with Transmission. With the first one you can access **Transmission** as if it was a regular Torrent client on your PC, with **Transmission Web**, you can access it through a web browser by accessing `http://serverip:9091`, login with your credentials and you're ready to go.