# NO-IP DUC

**NO-IP DUC** is a client that maps and updates the public IP of your server to a NO-IP host. This is particularly useful if you want to have a free hostname instead of having to use your IP directly or if your public IP is not static (which is the case for most people). For this you obviously need a NO-IP account, you can create one [here](https://www.noip.com/sign-up).

## Installation

To install this just follow this chain of commands:

``` text
cd /usr/local/src/
sudo wget http://www.no-ip.com/client/linux/noip-duc-linux.tar.gz
sudo tar xf noip-duc-linux.tar.gz
cd noip-2.1.9-1/
sudo make install
cd .. && sudo rm -rf noip*
```

This last command will ask for your login and password for the NO-IP site, it'll then ask you which of your hosts you want to update and in which interval.

## Auto-starting

We finally need to create a *systemctl* service so it can autostart on system boot. We make the service file with:

``` text
sudo nano /etc/systemd/system/noip2.service
```

Add the following text to the editor and save the file.

``` bash
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

``` text
systemctl daemon-reload
systemctl enable noip2.service
systemctl start noip2.service
```
