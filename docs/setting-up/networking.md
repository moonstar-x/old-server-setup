# Networking

Here's a table that contains the required default ports that need to be forwarded on the router (and allowed by the firewall) so the services are accessible from outside the private network. Ports can change if desired, port forwarding rules should be set up accordingly.

## Port Forwarding Table

| Service                     | Port Range  | Protocol |
|-----------------------------|-------------|----------|
| SSH                         | 22          | TCP      |
| Virtualbox - RDP            | 3389        | TCP      |
| TeamSpeak 3 - Voice         | 9987        | UDP      |
| TeamSpeak 3 - File Transfer | 30033       | TCP      |
| TeamSpeak 3 - ServerQuery   | 10011       | TCP      |
| OpenVPN                     | 1194        | UDP      |
| Squid                       | 1337        | TCP      |
| Plex                        | 32400       | TCP      |
| Transmission                | 9091        | TCP      |
| Source Games                | 27000-27050 | TCP/UDP  |
| Arma 3                      | 2302-2345   | TCP/UDP  |
| Assetto Corsa               | 9600        | TCP/UDP  |
| Assetto Corsa - HTTP        | 8081        | TCP      |
| Minecraft                   | 25565       | TCP      |
| Screeps                     | 21025       | TCP      |

If you don't know what internal IP the server is running on, you can always type on the terminal:

``` text
ifconfig
```

## UFW

**ufw** is a friendly frontend for **iptables** that makes it a lot easier to add connection rules to your firewall. One of the many wonders of **ufw** is the ability to add connection rules by specifying service names rather than ports (we'll see this when we'll set up the firewall rules for the next services).

### Installation

We'll need to install **ufw** and set it up.

``` text
sudo apt-get install ufw
sudo ufw default allow outgoing
sudo ufw default deny incoming
sudo ufw enable
```

### Usage

The commands should be pretty self-explanatory but, just in case, these default the firewall to allow any connections going from the server to the Internet and deny any incoming connections from the Internet to the server.

A very good command to check **ufw**'s status (if it's enabled or disabled) and see all the custom rules added and active is:

``` text
sudo ufw status
```

## SSH

### Installation

Since the server will run in headless mode, it would be very useful to be able to access it remotely from within (and even outside) the network. We'll use **OpenSSH**, it is generally installed with the OS but in case that it isn't, you can install it by running:

``` text
sudo apt-get install openssh-client openssh-server
```

### Setting-up

We now need to allow SSH connections through the firewall so we can access the server.

``` text
sudo ufw allow ssh
```

### Google Authentication (2FA)

Two Factor Authentication has become a must-have in terms of account security, almost all services out there have the option to add this security measure to ensure account security. Luckily, we can implement our own Two Factor Authentication to access the server through *SSH*.

#### Installation

To enable *2FA*, we'll need to install the following package:

``` text
sudo apt-get install libpam-google-authenticator
```

#### Setting-up

To set it up, simply run:

``` text
google-authenticator
```

When running this, you'll receive a *secret key* which is used to add your account manually to your phone's *2FA* application. Alternatively, you also get a nicely printed QR code on the terminal window (which you may need to resize to see fully) that you can scan with your phone. You will also get some scratch codes that you should always keep somewhere safe, just in case you lose access to your phone or something happens, you can still login to your server.

To continue, answer `y` to all the questions to set up *2FA* with the default settings.

We now need to enable *2FA* on *SSH*, to do this, edit the following file:

``` text
sudo nano /etc/ssh/sshd_config
```

Look for the following lines and edit them accordingly:

``` text
UsePAM yes
ChallengeResponseAuthentication yes
```

Save and close the editor and restart the SSH service.

``` text
sudo systemctl restart ssh
```

We now need to edit the PAM rule file:

``` text
sudo nano /etc/pam.d/sshd
```

At the end of the file, add the following line:

``` text
auth required pam_google_authenticator.so
```

Save and close the file.

#### Testing

In order to test that *2FA* works properly, open up a new *SSH* session without closing the previous one and try logging in, you'll be prompted for your user password and for the *2FA* code which is available on your phone.

!!! info
    When using *2FA* for *SSH*, all the users in the server will need to set-it up, otherwise they won't be able to access their accounts. In case you get locked out from one of these users, you can always login to a sudoer account (usually the admin one which is added when installing the OS) and force-login with: `sudo -iu <user>`.
