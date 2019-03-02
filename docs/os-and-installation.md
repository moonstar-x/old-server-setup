# OS and Installation

## Ubuntu Server 18.04 LTS 64-bit

Keep in mind this guide is for a server running an Ubuntu/Debian flavored distro. For performance and compatibility reasons, the OS of choice is [Ubuntu Server 18.04 LTS 64-bit](https://www.ubuntu.com/server) in headless mode. The installation is a quick and easy process that is assisted by it's own install wizard. When asked what snapshot (initial server configuration) should be installed, simply choose none or just default.

As a general rule of thumb, after installing the OS it is recommended to update the sources and packages:

    sudo apt-get update && sudo apt-get upgrade

## Sources

A little issue that comes with Ubuntu Server is the limited *apt* sources that come pre-installed, to fix this we'll need to replace the *sources.list* file inside */etc/apt/* with a more complete version.

    sudo -s
    > /etc/apt/sources.list
    nano /etc/apt/sources.list
    exit

Once the editor opens up, paste the following and save the file:

    # deb cdrom:[Ubuntu 18.04 LTS _Bionic Beaver_ - Release amd64 (20180426)]/ bionic main restricted
    # See http://help.ubuntu.com/community/UpgradeNotes for how to upgrade to
    # newer versions of the distribution.
    deb http://us.archive.ubuntu.com/ubuntu/ bionic main restricted
    # deb-src http://us.archive.ubuntu.com/ubuntu/ bionic main restricted

    ## Major bug fix updates produced after the final release of the
    ## distribution.
    deb http://us.archive.ubuntu.com/ubuntu/ bionic-updates main restricted
    # deb-src http://us.archive.ubuntu.com/ubuntu/ bionic-updates main restricted

    ## N.B. software from this repository is ENTIRELY UNSUPPORTED by the Ubuntu
    ## team. Also, please note that software in universe WILL NOT receive any
    ## review or updates from the Ubuntu security team.
    deb http://us.archive.ubuntu.com/ubuntu/ bionic universe
    # deb-src http://us.archive.ubuntu.com/ubuntu/ bionic universe
    deb http://us.archive.ubuntu.com/ubuntu/ bionic-updates universe
    # deb-src http://us.archive.ubuntu.com/ubuntu/ bionic-updates universe

    ## N.B. software from this repository is ENTIRELY UNSUPPORTED by the Ubuntu
    ## team, and may not be under a free licence. Please satisfy yourself as to
    ## your rights to use the software. Also, please note that software in
    ## multiverse WILL NOT receive any review or updates from the Ubuntu
    ## security team.
    deb http://us.archive.ubuntu.com/ubuntu/ bionic multiverse
    # deb-src http://us.archive.ubuntu.com/ubuntu/ bionic multiverse
    deb http://us.archive.ubuntu.com/ubuntu/ bionic-updates multiverse
    # deb-src http://us.archive.ubuntu.com/ubuntu/ bionic-updates multiverse

    ## N.B. software from this repository may not have been tested as
    ## extensively as that contained in the main release, although it includes
    ## newer versions of some applications which may provide useful features.
    ## Also, please note that software in backports WILL NOT receive any review
    ## or updates from the Ubuntu security team.
    deb http://us.archive.ubuntu.com/ubuntu/ bionic-backports main restricted universe multiverse
    # deb-src http://us.archive.ubuntu.com/ubuntu/ bionic-backports main restricted universe multiverse

    ## Uncomment the following two lines to add software from Canonical's
    ## 'partner' repository.
    ## This software is not part of Ubuntu, but is offered by Canonical and the
    ## respective vendors as a service to Ubuntu users.
    # deb http://archive.canonical.com/ubuntu bionic partner
    # deb-src http://archive.canonical.com/ubuntu bionic partner

    deb http://security.ubuntu.com/ubuntu bionic-security main restricted
    # deb-src http://security.ubuntu.com/ubuntu bionic-security main restricted
    deb http://security.ubuntu.com/ubuntu bionic-security universe
    # deb-src http://security.ubuntu.com/ubuntu bionic-security universe
    deb http://security.ubuntu.com/ubuntu bionic-security multiverse
    # deb-src http://security.ubuntu.com/ubuntu bionic-security multiverse

To finish, refresh your sources.

    sudo apt-get update