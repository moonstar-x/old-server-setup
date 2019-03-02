# Squid (HTTP Proxy)

**Squid** is an easy to set up HTTP proxy with the ability to do web-caching, meaning that if multiple users connect to the proxy and then try to download the same file, the process will be sped up for the second and next clients downloading because the file will be stored in the server's cache. An HTTP proxy is useful when you need to access IP whitelisted applications from other locations without the need to use a VPN.

## Installation

Anyways, to install **Squid**:

    sudo apt-get install squid apache2-utils

We'll install **apache2-utils** to generate users so we can limit the proxy connections by user authentication.

## Configuration

To set up said **Squid** users:

    sudo touch /etc/squid/squid_passwd
    sudo chown proxy /etc/squid/squid_passwd
    sudo htpasswd /etc/squid/squid_passwd $USER

!!! note
     Change **$USER** with the username you want to create, you'll be then asked to enter a password. Keep in mind you can create as many users as you need.

We can now edit **Squid**'s configuration:

    sudo nano /etc/squid/squid.conf

First we'll change the port that **Squid** will listen to (we like 1337/TCP for this). Look for and replace the following:

    http_port 1337

Now we'll tell **Squid** to add the user authentication parameters we need. Find the following, uncomment these parameters and edit them if needed.

    auth_param basic program /usr/lib/squid/basic_ncsa_auth /etc/squid/squid_passwd
    acl ncsa_users proxy_auth REQUIRED
    auth_param basic realm Squid proxy-caching web server
    auth_param basic credentialsttl 12 hours

Before finishing, we'll tell **Squid** to allow connections for authenticated users. Look for `http_access deny all` and above that line add the following:

    http_access allow ncsa_users

Now we'll create a new firewall rule to allow connections to the *1337/TCP* port.

    sudo ufw allow 1337/tcp

We can now restart **Squid**.

    sudo service squid restart

We're now ready to use our HTTP proxy, in your internet browser you can add your IP and the port you used to the proxy settings, if you try to connect with no authentication you'll get an error when browsing, you'll need to login with your user.
