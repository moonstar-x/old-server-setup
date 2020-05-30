# TeamSpeak 3

With the existence of Discord, it seems like **TeamSpeak 3** is losing it's popularity but for the sake of the server's utility, we'll still make a **TeamSpeak 3** server.

## Installation

Firstly, we need to make a new user.

``` text
sudo adduser --disabled-login teamspeak
sudo -iu teamspeak
```

We can now download the [newest version of TeamSpeak 3 Server](https://www.teamspeak.com/en/downloads#server) and extract it.

``` text
mkdir Downloads && cd Downloads
wget http://dl.4players.de/ts/releases/3.3.0/teamspeak3-server_linux_amd64-3.3.0.tar.bz2
tar xvf teamspeak3-server_linux_amd64-3.3.0.tar.bz2
```

!!! warning
    Keep in mind that the link above may be outdated.

Before starting the server, we need to accept **TeamSpeak 3**'s license agreement.

``` text
touch .ts3server_license_accepted
```

## Auto-starting

Now, we need to make a *systemctl* service so the server can run as a service and autostart with the server, this should be done in a sudoer user account. Create the daemon with:

``` text
sudo nano /lib/systemd/system/teamspeak.service
```

And in the text editor, paste the following and save.

``` text
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
```

We can now reload *systemctl* and start the service.

``` text
sudo systemctl --system daemon-reload
sudo systemctl enable teamspeak.service
sudo systemctl start teamspeak.service
```

The server is now up and running but before we continue, we need to set up some things.

## Configuration

We can get our server's privilege key so we can turn ourselves as the server admin.

``` text
cat /home/teamspeak/logs/ts3server_*
```

You'll receive an output similar to this one:

``` text
--------------------------------------------------------
ServerAdmin privilege key created, please use the line below
token=****************************************
--------------------------------------------------------
```

Lastly, let's not forget to add firewall rules to accept **TeamSpeak 3** connections.

``` text
sudo ufw allow 9987/udp
sudo ufw allow 30033/tcp
sudo ufw allow 10011/tcp
```

Finally, once you connect to your server, you'll be prompted to add the privilege key, enter it to become server admin and change any server configuration from within your client.
