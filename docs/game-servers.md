## SteamCMD
In this part we'll install **SteamCMD** which is a headless Steam client that lets us install Dedicated server packages without even the need to login (some exceptions apply).

### Installation
Before installing this tool, we'll create a new user, just to better organize ourselves.

    sudo adduser --disabled-login steam
    sudo -i -u steam

Since the *steam* user's *home* folder is empty, we'll make a folder for SteamCMD.

    mkdir steamcmd && cd steamcmd

Now we proceed with the download and extraction.

    wget https://steamcdn-a.akamaihd.net/client/installer/steamcmd_linux.tar.gz
    tar zxvf steamcmd_linux.tar.gz && rm steamcmd_linux.tar.gz

That's it, you can start up SteamCMD with:

    ./steamcmd.sh

### Usage
First and foremost, when using SteamCMD you'll need to login, whether it is to your account or to an *anonymous* account (which is basically the same as logging in to an empty user).

    login anonymous
    login <username>

> **Notice**: Use either one, prefer anonymous over your username when possible, keep in mind that some packages require a license for your account, meaning that certain game servers can only be downloaded if you have said game in your Steam library. When inserting your username you'll be prompted to enter your password and your Steam guard code in case you have 2 factor security enabled.

When downloading a package it is often recommended to save it in a separate folder for better organization.

    force_install_dir <path>

To download a package, you'll need it's AppID, a list of Dedicated sever AppIDs can be found [here](https://developer.valvesoftware.com/wiki/Dedicated_Servers_List).

    app_update <app_id> validate

For an example of downloading a package, you may want to check the [Arma 3 server installation](#arma-3-server).

## Arma 3 Server
### Installation
The *Arma 3* server will require a certain package to be able to run, you can install it using the following command in a sudoer user:

    sudo apt-get install lib32stdc++6

#### Modded Instance
To install the *Arma 3* server, you'll need SteamCMD and a Steam account that has a license for *Arma 3*. Remember to do the following commands with the *steam* user (you can switch using ```sudo -i -u steam```).

Head over to the folder where *SteamCMD* is located and run *SteamCMD*:

    login <username>

You'll be prompted for your password and your Steam Guard (2FA) code. Once you're in, change the directory where you want *Arma 3* to be installed (use absolute paths):

    force_install_dir /home/steam/arma3

You can now install *Arma 3*:

    app_update 233780

#### Vanilla Instance
In case you want to run a clean and modless server I recommend you to install the server again in another folder to avoid having mod folders inside and keys for mods you normally want in a modded instance. Luckily, you can easily do this with *SteamCMD*, login with your account like we did previously and change the installation directory to somewhere else:

    force_install_dir /home/steam/arma3vanilla

To install *Arma 3*:

    app_update 233780

### Configuring Server
Before any configuration process, we need to create the folders that we require to save the server profiles (where custom difficulties and vars are saved):

    mkdir -p ~/".local/share/Arma 3" && mkdir -p ~/".local/share/Arma 3 - Other Profiles"

#### Server Profile
In order to use a server profile where we'll set up the custom difficulties, we'll need to create a folder inside the previously created folder with a name for the server:

    cd ~/.local/share/Arma\ 3\ -\ Other\ Profiles/ && mkdir server && cd server

Here we can create the following file:

    nano server.Arma3Profile

You can paste the following config or change it to your liking:

    version=1;
    blood=1;
    singleVoice=0;
    gamma=1;
    brightness=1;
    maxSamplesPlayed=96;
    activeKeys[]=
    {
    };
    playedKeys[]=
    {
    };
    difficulty="Custom";
    class DifficultyPresets
    {
        class CustomDifficulty
        {
            displayName="Custom";
            optionDescription="Custom Difficulty";
            levelAI="AILevelMedium";
            class Options
            {
                reducedDamage=0;
                groupIndicators=1;
                friendlyTags=1;
                enemyTags=0;
                detectedMines=0;
                commands=1;
                waypoints=1;
                tacticalPing=0;
                weaponInfo=1;
                stanceIndicator=1;
                staminaBar=0;
                weaponCrosshair=0;
                visionAid=0;
                thirdPersonView=0;
                cameraShake=1;
                scoreTable=1;
                deathMessages=1;
                vonID=1;
                mapContent=1;
                autoReport=1;
                multipleSaves=1;
            };
            aiLevelPreset=1;
        };
        class CustomAILevel
        {
            skillAI=0.5;
            precisionAI=0.5;
        };
    };
    sceneComplexity=400000;
    shadowZDistance=100;
    viewDistance=1500;
    preferredObjectViewDistance=1000;
    terrainGrid=25;
    volumeCD=10;
    volumeFX=10;
    volumeSpeech=10;
    volumeVoN=10;
    vonRecThreshold=0.029999999;

#### Server Config
Now, we'll go to the folder where we installed *Arma 3* and create a config file with whatever name you wish:

    cd ~/arma3
    nano modded.cfg

You can paste the following config and change it or use whichever you want:

    steamPort = 2303;
    steamQueryPort = 2304;

    hostname = "Green Coast Gaming | Fun | Co-Op Missions | ACE & RHS & CUP";
    password = "";
    passwordAdmin = "SECRET";
    maxPlayers = 32;
    persistent = 0;

    disableVoN = 0;
    vonCodecQuality = 20;

    voteMissionPlayers = 1;
    voteThreshold = 0.66;
    allowedVoteCmds[] =
    {
        {"admin", false, false},
        {"kick", false, true, 0.51},
        {"missions", false, true},
        {"mission", false, false},
        {"restart", false, true},
        {"reassign", false, false}
    };

    timeStampFormat = "short";
    logFile = "server_console.log";

    BattlEye = 1;
    VerifySignatures = 2;
    kickDuplicate = 1;
    allowedFilePatching = 1;

    allowedLoadFileExtensions[] = {"hpp","sqs","sqf","fsm","cpp","paa","txt","xml","inc","ext","sqm","ods","fxy","lip","csv","kb","bik","bikb","html","htm","biedi"};
    allowedPreprocessFileExtensions[] = {"hpp","sqs","sqf","fsm","cpp","paa","txt","xml","inc","ext","sqm","ods","fxy","lip","csv","kb","bik","bikb","html","htm","biedi"};
    allowedHTMLLoadExtensions[] = {"htm","html","php","xml","txt"};

    onUserConnected = "";
    onUserDisconnected = "";
    doubleIdDetected = "";
    onUnsignedData = "kick (_this select 0)";
    onHackeddData = "kick (_this select 0)";

    headlessClients[] = {"127.0.0.1"};
    localClientp[] = {"127.0.0.1"};

As a side note, if you need to run servers with multiple configurations, you can always create more config files and use them at the server start.

### Mods
#### Setting Up Case Insensitive On Purpose (ciopfs)
On a Linux based installation, *Arma 3* modding becomes a bit complicated because of the Unix filesystem's case sensitivity, which renders mods with capital letters in their filenames unusable. Luckily we can fix this with a nice package called *ciopfs*. The following commands should be done through a sudoer user account.

    sudo apt-get install ciopfs

Now we'll create 2 folders where we'll install the mods and mount them with *ciopfs*:

    mkdir ~/arma3mods && mkdir ~/arma3mods_casesensitive
    ciopfs arma3mods arma3mods_casesensitive

This change will be undone on system restart, so in order to have the following set itself up on system start, we'll need to create a service. Create the following service file:

    sudo nano /etc/systemd/system/arma3ciopfs.service

Paste the following to the editor:

    Description=Arma 3 ciopfs mount.

    Wants=network.target
    After=syslog.target network-online.target

    [Service]
    Type=simple
    User=steam
    ExecStart=/usr/bin/ciopfs /home/steam/arma3mods /home/steam/arma3mods_casesensitive
    Restart=on-failure
    RestartSec=10
    KillMode=process

    [Install]
    WantedBy=multi-user.target

Restart *systemctl* and enable the service:

    sudo systemctl daemon-reload
    sudo systemctl enable arma3ciopfs.service
    sudo systemctl start arma3ciopfs.service

#### Installing Mods
Now we can go back to our *steam* user and start up *SteamCMD*. After logging in, you'll need to force the installation directory to the *arma3mods_casesensitive* folder that we created previously (while *ciopfs* is mounted).

    force_install_dir /home/steam/arma3mods_casesensitive

And install the mods that you need:

    workshop_download_item 107410 <mod_id>

After they've been installed, we'll need to symlinks from mod folders to our *Arma 3* server's directory. For this we'll need to access the *~/arma3mods* folder that we created previously (note that all the filenames here are lowercase).
For each mod we'll create a symlink from the id folder to the server folder with the mod name.

    ln -s ~/arma3mods/steamapps/workshop/content/107410/<mod_id> ~/arma3/@<mod_name>

As an example, for the **CBA_A3** mod:

    ln -s ~/arma3mods/steamapps/workshop/content/107410/450814997 ~/arma3/@CBA_A3

#### Updating Mods
For the sake of simplicity we will just use a simple *runscript* instead of a full-fledged wrapper script. For this we will go to directory where *SteamCMD* is installed and we will create our little *runscript*:

    nano update_arma3.txt

Inside the editor, paste the following, for each mod that you want to download/update you need to add a newline with the command `workshop_download_item 107410 <addon_id>`.

    ShutdownonFailedCommand 1
    @NoPromptForPassword 1
    force_install_dir /home/steam/arma3
    app_update 233780
    force_install_dir /home/steam/arma3mods_casesensitive
    // For every addon, do the following
    // workshop_download_item 107410 <addon_id>
    workshop_download_item 107410 463939057
    workshop_download_item 107410 333310405
    quit

Now, we can make a small *bash script* so that we can easily run this *runscript*.

    nano update_arma3.sh

Paste the following, make sure to change your username, here we're going to use the cached credentials so a password should not be required.

    #!/bin/bash
    ./steamcmd.sh +login <username> +runscript update_arma3.txt

Make it executable:

    chmod +x update_arma3.sh

Now, when we need to update the addons and the server files, just run the *./update_arma3.sh* script.

#### Mods List
Here's a small list of the mods that I currently use:

| Workshop ID | Addon Name                              | Folder Name              |
|-------------|-----------------------------------------|--------------------------|
| 463939057   | ACE                                     | @ACE                     |
| 450814997   | CBA_A3                                  | @CBA_A3                  |
| 621650475   | CUP ACE3 Compatibility Addon - Vehicles | @CUP_ACE_compat_vehicles |
| 549676314   | CUP ACE3 Compatibility Addon - Weapons  | @CUP_ACE_compat_weapons  |
| 583496184   | CUP Terrains - Core                     | @CUP_Core                |
| 853743366   | CUP Terrains - CWA                      | @CUP_CWA                 |
| 583544987   | CUP Terrains - Maps                     | @CUP_Maps                |
| 497661914   | CUP Units                               | @CUP_Units               |
| 541888371   | CUP Vehicles                            | @CUP_Vehicles            |
| 497660133   | CUP Weapons                             | @CUP_Weapons             |
| 333310405   | Enhanced Movement                       | @Enhanced_Movement       |
| 843425103   | RHSAFRF                                 | @RHSAFRF                 |
| 843593391   | RHSGREF                                 | @RHSGREF                 |
| 843632231   | RHSSAF                                  | @RHSSAF                  |
| 843577117   | RHSUSAF                                 | @RHSUSAF                 |
| 498740884   | SharkTac User Interface                 | @SharkTac_UI             |
| 620019431   | TaskForce Arrowhead Radio               | @task_force_radio        |

### Custom Missions
In order to run custom missions on your server, you'll need to add the *.pbo* files inside the *mpmissions* folder inside the *Arma 3* server folder. You can get the *.pbo* files directly from Steam Workshop if you download the missions through this [Workshop Downloader](https://steamworkshopdownloader.io/) or from [Armaholic](http://www.armaholic.com/list.php?c=arma3_files_scenarios_mpmissions).

### Firewall Rules
Again, we'll need to add the following firewall rules:

    sudo ufw allow 2302:2345/tcp
    sudo ufw allow 2302:2345/udp

### Running the Server
Now, to run the game, we'll create a small script so we can easily save the addons that we want to run and the configs that we want to load. Inside the *Arma 3* server directory, create the script file (the filename with whatever you want):

    nano start_<name>.sh

Inside the editor, paste the following:

    #!/bin/bash
    ./arma3server -name=<server_name> -config=<config_file> -mod=@mod1\;@mod2\;@mod3...

> *Notice*: *server_name* refers to the server name that you used for the *Arma 3 Profile* that is located in **~/.local/share/Arma 3 - Other Profiles**, the *config_file* refers to the *Arma 3 Config* that is inside the *Arma 3* directory, replace them with your own settings.

An example for this script that runs ACE and CBA_A3.

    #!/bin/bash
    ./arma3server -name=server -config=modded.cfg -mod=@CBA_A3\;@ACE 

As per always, the script must be made executable:

    chmod +x <script_name>.sh

## DayZ Standalone Server
Unfortunately, the DayZ Standalone Server for Linux hasn't been released yet, it should come anytime soon though. In the meantime, this section remains as a placeholder.

## Assetto Corsa
### Installation
To install the **Assetto Corsa** dedicated server we'll need to start SteamCMD using a platform flag to set our platform as *Windows*, basically telling **SteamCMD** to download the Windows version of the packages we want to download.

First we'll change to our *steam* user and go to where our SteamCMD is located.

    sudo -i -u steam
    cd ~/Steam

We'll run SteamCMD with the following argument:

    ./steamcmd.sh +@sSteamCmdForcePlatformType windows

We can now login with our username, we'll be prompted for our password and two factor code if enabled.

    login <username>

We'll change the installation folder:

    force_install_dir /home/steam/assettocorsa

And now we download the required files.

    app_update 302550

Once we finish downloading we can exit **SteamCMD** and go to the folder where **Assetto Corsa** is installed.

    cd ~/assettocorsa

### Configuration
The configuration of the server is pretty self-explanatory in terms of settings. To change the hostname, the track, the cars, etc.. Edit the following file:

    nano cfg/server_cfg.ini

To change the car list:

    nano cfg/entry_list.ini

Make sure your ports are forwarded and that the firewall isn't blocking any connections Assetto Corsa may need, the server will not start up if it can't contact Assetto Corsa's main server.

We'll add some firewall rules as a sudoer user (the administrator user for example):

    sudo ufw allow 8081/tcp
    sudo ufw allow 9600/tcp
    sudo ufw allow 9600/udp

> **Notice**: If you wish to run multiple servers or multiple configurations it is advised to reinstall the server in a different location and run a different acServer executable, after all, the server file size isn't too big.

If you want to add mods to the server, you can get them from [here](https://assettocorsa.club/en/), make sure to place them in the correct folder: `./content/cars` or `./content/tracks`, keep in mind the clients will also need the mods installed.

### Running the Server
You can start up the server with:

    ./acServer

## Minecraft
### Installing Requirements
The following installs a vanilla **Minecraft** server, if you're looking for a modded server you'll have to look up *Forge* or *CraftBukkit* guides. **Minecraft** as we know requires Oracle Java in order to run. Ubuntu does not come with Java installed nor is it available on the default package repos, so we'll need to add a PPA repo and install it from there, just do the following:

    sudo apt-add-repository ppa:webupd8team/java
    sudo apt-get update
    sudo apt-get install oracle-java8-installer

### Installation
After installing Oracle Java, we're ready to install a **Minecraft** server. We'll change to our *steam* user since it's dedicated to our game servers. We'll also create a folder specifically for it.

    sudo -i -u steam
    cd ~
    mkdir minecraft && cd minecraft

Head over to the [official Minecraft website](https://minecraft.net/en-us/download/server) to download the newest **Minecraft** server executable and start it up.

    wget https://launcher.mojang.com/mc/game/1.13/server/d0caafb8438ebd206f99930cfaecfa6c9a13dca0/server.jar
    java -Xms2G -Xmx2G -jar server.jar nogui

> Replace the first two arguments with the amount of RAM you want to dedicate for the Minecraft server.

If everything went well, the server closed itself and a little *eula.txt* file was generated in the server's directory. We need to edit that file to accept the EULA agreement.

    nano eula.txt

Change the last line to:

    eula=true

Now fire up the server again and stop it once it finishes loading up.

    java -Xms2G -Xmx2G -jar server.jar nogui
    stop

### Configuration
Now that all the required files have been generated, we can continue to set up our server's configuration.

    nano server.properties

All the settings here are pretty much self explanatory but there's one that I'm going to address and it's *online-mode*. This setting tells the **Minecraft** server whether it should connect to the Minecraft.net servers to check if all the users connecting have a **Minecraft** Premium account and download their respective skins. This option is generally set to *true*, but if we have friends that do not have **Minecraft** Premium they'll be able to join our server if we set *online-mode=false*, keep in mind that everyone's skins will be set to default and anyone that joins through a 3rd-party launcher may be able to change their name anytime (risking identity theft or ban avoidance).

Remember, we need to allow connections through the firewall (you may need to change to an user that has access to the *sudo* command, i.e you main administrator user):

    sudo ufw allow 25565/tcp

We'll now create a script to run the server a lot easier. Create a file running:

    nano start.sh

Paste the following:

    #!/bin/bash
    java -Xms2G -Xmx2G -jar server.jar nogui

Make it executable:

    chmod +x start.sh

### Running the Server
You can now start the server just by running the start script.

    ./start.sh

## Screeps
### Installing Requirements
The following game server requires **Node.js**, in order to install it, we'll run the following commands:

    curl -sL https://deb.nodesource.com/setup_11.x | sudo -E bash -
    sudo apt-get install nodejs

Apart from **Node.js** we will also need the basic **Build Essentials** and **Python**.

    sudo apt-get install build-essential python

### Installation
To install *Screeps*, we will switch to our *steam* user where we store our game servers and we'll create a folder in our home directory.

    sudo -i -u steam
    mkdir screeps && cd screeps

Here we can install *Screeps*.

    npm install screeps

Before starting the server, we will need a *Steam Web API Key*, head over to [this link](https://steamcommunity.com/dev/apikey), copy it to your clipboard and paste it when it's required.

    npx screeps init

After doing this you can now start the server.

    npx screeps start

As per usual, we will need to add some firewall rules (as a sudoer):

    sudo ufw allow 21025/tcp