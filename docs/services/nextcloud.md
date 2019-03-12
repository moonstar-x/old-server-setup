# Nextcloud (Self-hosted Cloud)

Nextcloud is a very good option when it comes to self-hosted cloud services, it is a fork of Owncloud, it has more features available and it can also be installed with Snap.

## Installation

Since Nextcloud can be installed with Snap, we'll use this to heavily simplify the set-up process. With Snap, we save ourselves from the hassle of having to configure Apache/nginx and php.

First, we'll install Nextcloud with Snap:

    sudo snap install nextcloud

By default, Nextcloud from Snap cannot access removable devices, for this we'll need to connect the required module:

    sudo snap connect nextcloud:removable-media

## Setting-up The Data Folder

We'll install Nextcloud with the web interface, for this we'll need to change the auto-configure script to manually select the data folder (otherwise it would default to `/var/snap/nextcloud/common/nextcloud/data`). We're going to change the data folder to `/media/sata_2tb/Cloud`, for this just open the following file:

    sudo nano /var/snap/nextcloud/current/nextcloud/config/autoconfig.php

And change the following variable with the path of the data folder you wish to use. Make sure this folder doesn't exist to let Nextcloud create it with the required ownership/permissions.

    'directory' => '/media/my/hdd',

After changing this variable, restart the *php-fpm* service:

    sudo snap restart nextcloud.php-fpm

## Configuring Ports

Before trying to access the web interface we'll need to set-up the ports that Nextcloud will use. By default it uses port **HTTP/80** and **HTTPS/443**. In case you need to change those ports, use the following command:

    sudo snap set nextcloud ports.http=80 ports.https=443

Make sure you allow them through the firewall:

    sudo ufw allow 80/tcp
    sudo ufw allow 443/tcp

## Setting-up HTTPS

As a security measure, we'll enable *HTTPS* to access the web interface. For this we'll use [Let's Encrypt](https://letsencrypt.org/) to generate an *SSL Certificate*. This services requires you to have a domain that points to the IP of the server and have ports *80* and *443* open. In case you don't have a domain you can always use a [self-signed certificate](https://docs.nextcloud.com/server/14/admin_manual/installation/source_installation.html#enabling-ssl-label), though this method will most likely display a warning on the browser of the client accessing the web interface.

To enable *HTTPS* with Let's Encrypt:

    sudo nextcloud.enable-https lets-encrypt

When running this command you'll be prompted with a contact email address and the domain used to access the server.

## Additional Settings

As an additional setting, we'll change the available memory that php can use (if php ever runs out of memory, file thumbnails will most likely disappear and the server may experience unexpected behavior). We'll set this to *512MB* of RAM.

    sudo snap set nextcloud php.memory-limit=512M

## Creating Admin Account and Final Touches

Lastly, we'll create an admin account. Access the web interface through an Internet browser and by visiting the domain that you used previously when setting-up *HTTPS*, trying to access the service through a different address may result in a "untrusted access address" error.

Once you visit the web interface, you'll be prompted to select an username and password, this account will be an administrator. Once this is finished you'll be redirected to your cloud dashboard.

### Letting Users Register

In a vanilla installation there's no way for users to register on Nextcloud, for this we'll use an app called [Registration](https://apps.nextcloud.com/apps/registration). Simply install it on your Nextcloud and the plugin will be automatically enabled: when a user visits the web interface they'll have an option to register.