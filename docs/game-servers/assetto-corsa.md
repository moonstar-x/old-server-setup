# Assetto Corsa

For this server we're going to approach two different alternatives: running the server directly from the Linux host or running it under a Windows VM. Each of these alternatives have their own pros and cons.

## Linux Binary Alternative

Running the server directly with the Linux binary saves us a lot of disk space (almost 20GB since you don't need to install the game binaries) but will need to be configured manually through the server configs which can be a bit complicated when there is a lot of custom content and/or you use multiple configs for the server.

### Installation

To install the **Assetto Corsa** dedicated server we'll need to start SteamCMD using a platform flag to set our platform as *Windows*, basically telling **SteamCMD** to download the Windows version of the packages we want to download.

First we'll change to our *steam* user and go to where our SteamCMD is located.

``` text
sudo -iu steam
cd ~/Steam
```

We'll run SteamCMD with the following argument:

``` text
./steamcmd.sh +@sSteamCmdForcePlatformType windows
```

We can now login with our username, we'll be prompted for our password and two factor code if enabled.

``` text
login <username>
```

We'll change the installation folder:

``` text
force_install_dir /home/steam/assettocorsa
```

And now we download the required files.

``` text
app_update 302550
```

Once we finish downloading we can exit **SteamCMD** and go to the folder where **Assetto Corsa** is installed.

``` text
cd ~/assettocorsa
```

### Configuration

The configuration of the server is pretty self-explanatory in terms of settings. To change the hostname, the track, the cars, etc.. Edit the following file:

``` text
nano cfg/server_cfg.ini
```

To change the car list:

``` text
nano cfg/entry_list.ini
```

For an easier way to create these configuration files I recommend you to use the Windows version of the server to generate these configs on a GUI window and then transfer the *.ini* files generated to the server.

Make sure your ports are forwarded and that the firewall isn't blocking any connections Assetto Corsa may need, the server will not start up if it can't contact Assetto Corsa's main server.

We'll add some firewall rules as a sudoer user (the administrator user for example):

``` text
sudo ufw allow 8081/tcp
sudo ufw allow 9600/tcp
sudo ufw allow 9600/udp
```

!!! note
    If you wish to run multiple servers or multiple configurations it is advised to reinstall the server in a different location and run a different acServer executable, after all, the server file size isn't too big.

If you want to add mods to the server, you can get them from [here](https://assettocorsa.club/en/), make sure to place them in the correct folder: `./content/cars` or `./content/tracks`, keep in mind the clients will also need the mods installed.

### Running the Server

You can start up the server with:

``` text
./acServer
```

## Windows Virtual Machine Alternative

The biggest downside of running the server through a Windows VM is the "wasted" disk space, since you need to count the Windows installation and the Assetto Corsa game binaries which both summed take around 40 GB. The great advantage that this has though, is the ease of configuration for custom content and multiple server configurations thanks to the nice server manager GUI. For this reason, I have opted for this option but you can always choose the one you prefer.

### Installing the Virtual Machine

For the virtual machine we're going to use VirtualBox, I'm assuming you already have it installed but in case you haven't check [this section of the guide](../setting-up/packages-and-programs.md#virtualbox).

We're going to install Windows 7 Home Basic, we're going to give it 2GB or RAM, 60GB of disk space and access to 2 CPU cores. We're going to do this on the *steam* user, for this, we'll need to add the *steam* user to the *vboxusers* group.

``` text
sudo adduser steam vboxusers
```

Now, we'll change to the *steam* user.

``` text
sudo -iu steam
```

To set-up the VM (make sure to change the settings to your needs):

``` text
vboxmanage createvm --name "Assetto-win" --register
vboxmanage modifyvm "Assetto-win" --memory 2048 --acpi on --ioapic on --boot1 dvd --nic1 bridged --bridgeadapter1 enp0s3 --nictype1 82540EM --vram 128 --cpus 2 --vrde on
vboxmanage createhd --filename ~/VirtualBox\ VMs/Assetto-win/assetto-win.vdi --size 100000
vboxmanage storagectl "Assetto-win" --name "IDE Controller" --add ide
vboxmanage storageattach "Assetto-win" --storagectl "IDE Controller" --port 0 --device 0 --type hdd --medium ~/VirtualBox\ VMs/Assetto-win/assetto-win.vdi
vboxmanage storageattach "Assetto-win" --storagectl "IDE Controller" --port 1 --device 0 --type dvddrive --medium ~/Downloads/Windows7.iso
vboxheadless --startvm “Assetto-win”
```

At this point, the VM should be successfully running, to control it, connect to the host's IP through an RDP connection (such as Microsoft Remote Desktop). Continue with the Windows installation process. Once the installation is complete, we'll install the VirtualBox Guest Additions, in the host, run the following command:

``` text
vboxmanage storageattach "Assetto-win" --storagectl "IDE Controller" --port 1 --device 0 --type dvddrive --medium /usr/share/virtualbox/VBoxGuestAdditions.iso
```

The installation drive should show up on *My Computer*, install it and restart the guest.

Once rebooted and while the guest is running, type the following commands on the host:

``` text
vboxmanage controlvm "Assetto-win" setvideomodehint 1280 720 32
vboxmanage storageattach "Assetto-win" --storagectl "IDE Controller" --port 1 --device 0 --type dvddrive --medium emptydrive
```

### Installing the Server

To install the server we'll need to download [SteamCMD](https://steamcdn-a.akamaihd.net/client/installer/steamcmd.zip) and install [.NET 4 Framework](https://dotnet.microsoft.com/download/dotnet-framework-runtime).

For *SteamCMD*, we'll create a folder in our main drive `C:\steamcmd` and we'll add this folder to our path:

1. Open the Start Menu, right click *My Computer* and select *Properties*.
2. On the left tab select *Advanced system settings* and click on *Environment Variables...*.
3. On the *Path* variable, add the following: `;C:\steamcmd`.

Now that *SteamCMD* is in our *Path*, we can easily run it through a command line prompt directly with the `steamcmd` command. We'll use a small *bat* script to download and update *Assetto Corsa*. Open up a notepad and save it as `update_assetto.bat`. Inside the text editor add the following text (make sure to replace your *%username%* with your actual username):

``` shell
@echo off
steamcmd +login %username% +force_install_dir c:\assettocorsa +app_update 244210 +quit
```

Running this script will automatically download the server files. To run the server open up `C:\assettocorsa\server\acServerManager.exe` and configure your server through the GUI. To add custom content, add it to the `C:\assettocorsa\` directory.
