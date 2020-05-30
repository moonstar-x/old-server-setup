# Packages and Programs

This is a list that contains a number of useful packages and programs that will become helpful when using the server.

## Screen

**Screen** is a wonderful tool capable of improving multitasking on a single shell window with the ability to *background* processes. It also lets you recover shell windows when there's a connection loss.

### Installation

Installing **screen** is as simple as running the following command:

    sudo apt-get install screen

### Usage

At first it may seem a little complicated and maybe even intimidating to use this tool but once you get used to it you'll realize how useful it really is.

First start up **screen** by typing:

    screen -S <socket name>

Here's a table with the keys and actions you can use with **screen**:

| Keys         | Action                    |
|--------------|---------------------------|
| `Ctrl-a` `c` | Creates a new window.     |
| `Ctrl-a` `n` | Switches between windows. |
| `Ctrl-a` `d` | Detaches from screen.     |
| `Ctrl-a` `n` | Switches between screens. |

To reattach to a *screen*:

    screen -r <screen pid>

or,

    screen -rd <screen socket name>

## VirtualBox

**VirtualBox** is a machine virtualization tool which has the option to run in headless mode (pretty useful in this case), which directs all display and controls through an *RDP* connection. Not only can we run some VM's to run certain services that may need a dedicated environment, but we can also "portabilize" these servers, making it easier to back them up in case something goes wrong.

In this case we'll use a VM to manage our router in a graphical environment without the need to install a DE or WM on the server (since we want it to remain headless). This VM doesn't require to run 24/7 and ends up being pretty useful considering the *only* alternative would be to forward **X11** through **SSH** which is unstable and literally unusable in high latency connections.

### Installation

Firstl, we need to install some initial packages and reboot the server.

    sudo apt install build-essential dkms
    sudo reboot

We now need to add the **VirtualBox** source to our *apt* sources and refresh them.

    sudo echo "deb http://download.virtualbox.org/virtualbox/debian bionic contrib" >> /etc/apt/sources.list
    sudo apt-get update

Right before the installation we need to install Oracle's public key.

    wget -q https://www.virtualbox.org/download/oracle_vbox_2016.asc -O- | sudo apt-key add -

We're ready to install **VirtualBox**.

    sudo apt-get install virtualbox-5.2

Before creating/running our VM's we'll need to add our user to the *vboxusers* user group. *(Replace $USER with your username.)*

    sudo adduser $USER vboxusers

We're not there yet, we still need to install the extension that will let **VirtualBox** run a *VRDP* server so we can use the VM remotely through an *RDP* connection. [Download the VirtualBox Extension Pack corresponding to your version from Oracle's official site](https://download.virtualbox.org/virtualbox/). To check your **VirtualBox** version, run:

    vboxmanage --version

Copy the link and download through *wget* and install. (Keep in mind the example may show a link incompatible with your **VirtualBox** version).

    wget https://download.virtualbox.org/virtualbox/5.2.16/Oracle_VM_VirtualBox_Extension_Pack-5.2.16.vbox-extpack
    sudo VBoxManage extpack install Oracle_VM_VirtualBox_Extension_Pack-5.2.16.vbox-extpack

### Creating a VM

Creating a VM is done through a series of commands, the process can be a little long to write so keep an eye of what you're about to input in your terminal window. As an example we'll install [Lubuntu 18.04 64-bit](https://lubuntu.net/downloads/). Download the *iso* file and keep it somewhere (for example in **~/Downloads**).

Before we begin, we need to see what is our network interface, which is usually called *eth0 enp0s3 wlan0* etc, depending on how many interfaces the server has or interface type. To see your interface name type in console:

    ifconfig

You should receive an output similar to this one:

    enp0s3: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
            inet 192.168.0.15  netmask 255.255.255.0  broadcast 192.168.0.255
            inet6 ::a00:27ff:fe49:8561  prefixlen 64  scopeid 0x0<global>
            inet6 fe80::a00:27ff:fe49:8561  prefixlen 64  scopeid 0x20<link>
            ether 08:00:27:49:85:61  txqueuelen 1000  (Ethernet)
            RX packets 755025  bytes 1132393073 (1.1 GB)
            RX errors 0  dropped 0  overruns 0  frame 0
            TX packets 94785  bytes 9330879 (9.3 MB)
            TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

In our case *enp0s3* is the name of our interface, **it may be different for you**.

Now we begin. Feel free to change whatever to accommodate your needs.

    VBoxManage createvm --name "Lubuntu" --register
    VBoxManage modifyvm "Lubuntu" --memory 2048 --acpi on --boot1 dvd --nic1 nat --vram 128 --cpus 2
    VBoxManage createhd --filename ~/VHDD/Lubuntu.vdi --size 10000
    VBoxManage storagectl "Lubuntu" --name "IDE Controller" --add ide
    VBoxManage storageattach "Lubuntu" --storagectl "IDE Controller" --port 0 --device 0 --type hdd --medium ~/VHDD/Lubuntu.vdi
    VBoxManage storageattach "Lubuntu" --storagectl "IDE Controller" --port 1 --device 0 --type dvddrive --medium ~/Downloads/lubuntu-18.04-desktop-amd64.iso
    VBoxManage modifyvm "Lubuntu" --vrde on

That's it, the VM is ready to be used, but before we go ahead and run it, we need to add some firewall rules so we can access the *VRDP* server.

    sudo ufw allow 3389/tcp

### Running the VM

You can start your Headless VM with the following (keep in mind to replace with your own VM name):

    VBoxHeadless --startvm "Lubuntu"

To control your VM, connect to your server's IP through a *RDP* client. (Remote Desktop on Windows, for example)

Should you need to power off, reset or suspend your VM, use the following:

    VBoxManage controlvm "Lubuntu" poweroff
    VBoxManage controlvm "Lubuntu" pause
    VBoxManage controlvm "Lubuntu" reset

## Others

These are other packages which may be required by the services that run on the machine.

    sudo apt-get install lib32gcc1 ranger unzip wget thunar

!!! note
     **lib32gcc1** is required by **SteamCMD** to be able to properly run certain game servers on a 64-bit architecture.
