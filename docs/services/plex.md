# Plex

**Plex** is an amazing tool that lets you run a media server on your computer. You can add your own music, movies, TV series or even pictures and stream them from an Internet browser or a mobile device (with the Plex app). This is pretty useful for all of us who keep a collection of movies or TV series DVD's and want them in a single place. The best feature this program has is the ability to share your library with your friends.

## Installation

Unlike the other programs, this one installs in a pretty easy way. First, head over to the [Plex downloads site](https://www.plex.tv/media-server-downloads/#plex-media-server) and download the latest release for your operating system. *(Keep in mind the link in the example may be outdated.)*

    wget https://downloads.plex.tv/plex-media-server/1.13.5.5291-6fa5e50a8/plexmediaserver_1.13.5.5291-6fa5e50a8_amd64.deb

Now we proceed with the installation:

    sudo dpkg -i plexmediaserver_1.13.5.5291-6fa5e50a8_amd64.deb
    sudo systemctl enable plexmediaserver.service
    sudo systemctl start plexmediaserver.service

Just like the other programs, we need to add a firewall rule.

    sudo ufw allow 32400/tcp

We can now access the web interface via `http://serverip:32400/web` to set up the server and link it to our Plex account.

## Adding Multiple Hard Drives

Maybe your media collection is so big that you cannot fit it in one single drive, you insert a second hard drive to your computer, you plug in your USB hard drive only to find out that when trying to add it to your Plex library you get bombarded with errors. This issue happens when there's some problems with the permissions system and the *plex* user.

In order to fix this issue we'll need to change some settings with the *plex* user, enter the following commands (replace **$USER** with your actual username):

    sudo gpasswd -a plex plugdev
    sudo gpasswd -a plex root
    sudo gpasswd -a plex sudo
    sudo gpasswd -a plex $USER
    sudo gpasswd -a $USER plex

Now we'll add our hard drives to the mount points that we'll create so they get mounted and recognized by **Plex** on startup. In our case we'll add an internal (SATA) drive and an external (USB) drive.

    mkdir /media/plex_sata /media/plex_usb

I'll assume your drives are already partitioned and formatted. We can list the drives with:

    sudo blkid

You'll receive an output similar to this one:

    /dev/sda2: UUID="f5a613fe-981a-11e8-937c-080027498561" TYPE="ext4" PARTUUID="544fba45-03a8-4b4f-a199-26f6b1b1d94b"
    /dev/loop0: TYPE="squashfs"
    /dev/sda1: PARTUUID="46e94feb-2ed3-4b1f-b21f-41d452fe2b13"

!!! note
     Notice here that there's only one hard drive plugged in so it only recognizes /dev/sda, this is called the ID. If you have a drive plugged in and it does not show up in this list, it is likely not partitioned or formatted. Do not proceed if this happens. Instead use **fdisk** to fix your issues and then come back.

Identify the drive UUID that corresponds with what you need to add to **Plex**, copy the one displayed, we'll need it later. We now need to edit our file system table.

 **PROCEED AT YOUR OWN RISK, MESSING UP THIS FILE WILL MOST PROBABLY BREAK YOUR COMPUTER!**

 Access the *fstab* file:

    sudo nano /etc/fstab

We now need to add the following in a new line (separate everything with a `Tab`).

    UUID=$UUID  $DIR    $FORMAT 0   0

If you replace everything with your information you should get something like this.

    UUID=A82CC6B45D20  /media/plex_usb    ntfs    0   0

Finally, reboot your server. You can now add your external hard drive in the **Plex** library by adding the folder you added as a mount point.
