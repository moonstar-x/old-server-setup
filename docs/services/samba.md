# Samba (SMB/CIFS)

**Samba** lets your Linux based server share files and folders on a Windows File Sharing Workgroup using the same protocol (SMB/CIFS), this is pretty useful when you need to share files between computers on your network and can even be accessed through the Internet (although it should only be done through a VPN for security purposes).

## Installation

To install **Samba**:

    sudo apt-get install samba

## Configuring

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