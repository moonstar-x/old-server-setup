# Arma 3 Server

## Installation

The *Arma 3* server will require a certain package to be able to run, you can install it using the following command in a sudoer user:

``` text
sudo apt-get install lib32stdc++6
```

### Modded Instance

To install the *Arma 3* server, you'll need SteamCMD and a Steam account that has a license for *Arma 3*. Remember to do the following commands with the *steam* user (you can switch using `sudo -iu steam`).

Head over to the folder where *SteamCMD* is located and run *SteamCMD*:

``` text
login <username>
```

You'll be prompted for your password and your Steam Guard (2FA) code. Once you're in, change the directory where you want *Arma 3* to be installed (use absolute paths):

``` text
force_install_dir /home/steam/arma3
```

You can now install *Arma 3*:

``` text
app_update 233780
```

### Vanilla Instance

In case you want to run a clean and mod-less server I recommend you to install the server again in another folder to avoid having mod folders inside and keys for mods you normally want in a modded instance. Luckily, you can easily do this with *SteamCMD*, login with your account like we did previously and change the installation directory to somewhere else:

``` text
force_install_dir /home/steam/arma3vanilla
```

To install *Arma 3*:

``` text
app_update 233780
```

## Configuring Server

Before any configuration process, we need to create the folders that we require to save the server profiles (where custom difficulties and vars are saved):

``` text
mkdir -p ~/".local/share/Arma 3" && mkdir -p ~/".local/share/Arma 3 - Other Profiles"
```

### Server Profile

In order to use a server profile where we'll set up the custom difficulties, we'll need to create a folder inside the previously created folder with a name for the server:

``` text
cd ~/.local/share/Arma\ 3\ -\ Other\ Profiles/ && mkdir server && cd server
```

Here we can create the following file:

``` text
nano server.Arma3Profile
```

You can paste the following config or change it to your liking:

``` text
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
```

### Server Config

Now, we'll go to the folder where we installed *Arma 3* and create a config file with whatever name you wish:

``` text
cd ~/arma3
nano modded.cfg
```

You can paste the following config and change it or use whichever you want:

``` text
steamPort = 2303;
steamQueryPort = 2304;

hostname = "SERVER NAME";
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
```

As a side note, if you need to run servers with multiple configurations, you can always create more config files and use them at the server start.

## Mods

### Setting Up Case Insensitive On Purpose (ciopfs)

On a Linux based installation, *Arma 3* modding becomes a bit complicated because of the Unix filesystem's case sensitivity, which renders mods with capital letters in their filenames unusable. Luckily we can fix this with a nice package called *ciopfs*. The following commands should be done through a sudoer user account.

``` text
sudo apt-get install ciopfs
```

Now we'll create 2 folders where we'll install the mods and mount them with *ciopfs*:

``` text
mkdir ~/arma3mods && mkdir ~/arma3mods_casesensitive
ciopfs arma3mods arma3mods_casesensitive
```

This change will be undone on system restart, so in order to have the following set itself up on system start, we'll need to create a service. Create the following service file:

``` text
sudo nano /etc/systemd/system/arma3ciopfs.service
```

Paste the following to the editor:

``` text
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
```

Restart *systemctl* and enable the service:

``` text
sudo systemctl daemon-reload
sudo systemctl enable arma3ciopfs.service
sudo systemctl start arma3ciopfs.service
```

### Installing Mods

Now we can go back to our *steam* user and start up *SteamCMD*. After logging in, you'll need to force the installation directory to the *arma3mods_casesensitive* folder that we created previously (while *ciopfs* is mounted).

``` text
force_install_dir /home/steam/arma3mods_casesensitive
```

And install the mods that you need:

``` text
workshop_download_item 107410 <mod_id>
```

After they've been installed, we'll need to create symlinks from the mod folders to our *Arma 3* server's directory. For this we'll need to access the *~/arma3mods* folder that we created previously (note that all the filenames here are lowercase).
For each mod we'll create a symlink from the id folder to the server folder with the mod name.

``` text
ln -s ~/arma3mods/steamapps/workshop/content/107410/<mod_id> ~/arma3/@<mod_name>
```

As an example, for the **CBA_A3** mod:

``` text
ln -s ~/arma3mods/steamapps/workshop/content/107410/450814997 ~/arma3/@CBA_A3
```

### Updating Mods

For the sake of simplicity we will just use a simple *run-script* instead of a full-fledged wrapper script. For this we will go to directory where *SteamCMD* is installed and we will create our little *run-script*:

``` text
nano update_arma3.txt
```

Inside the editor, paste the following, for each mod that you want to download/update you need to add a newline with the command `workshop_download_item 107410 <addon_id>`.

``` text
@ShutdownonFailedCommand 1
@NoPromptForPassword 1
force_install_dir /home/steam/arma3
app_update 233780
force_install_dir /home/steam/arma3mods_casesensitive
// For every addon, do the following
// workshop_download_item 107410 <addon_id>
workshop_download_item 107410 463939057
workshop_download_item 107410 333310405
quit
```

!!! tip
    As a safety measure, you should add the same mod multiple times to avoid mod download failures.

Now, we can make a small *bash* script so that we can easily run this *run-script*.

``` text
nano update_arma3.sh
```

Paste the following, make sure to change your username, here we're going to use the cached credentials so a password should not be required.

``` text
#!/bin/bash
./steamcmd.sh +login <username> +runscript update_arma3.txt
```

Make it executable:

``` text
chmod +x update_arma3.sh
```

Now, when we need to update the addons and the server files, just run the *./update_arma3.sh* script.

### Mods List

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
| 501966277   | Zombies and Demons                      | @Zombies_and_Demons      |

### Infinite Loading Screen Bug

Unfortunately, the Linux Arma 3 server can be a bit buggy, this is the case for servers that run certain mod combinations (in this case CUP and RHS) that will make clients get stuck on an infinite loading screen when joining the server, the server console will not display any error messages and will in fact freeze the process. It seems the only way to fix this issue is to disable BattlEye from the server configs. In the server config, change the following:

``` text
BattlEye = 0;
```

This will in fact turn off BattlEye on your server which will make it less secure against hackers, sadly it is the only fix for this weird problem.

## Custom Missions

In order to run custom missions on your server, you'll need to add the *.pbo* files inside the *mpmissions* folder inside the *Arma 3* server folder. You can get the *.pbo* files directly from Steam Workshop if you download the missions through this [Workshop Downloader](https://steamworkshopdownloader.io/) or from [Armaholic](http://www.armaholic.com/list.php?c=arma3_files_scenarios_mpmissions).

## Firewall Rules

Again, we'll need to add the following firewall rules:

``` text
sudo ufw allow 2302:2345/tcp
sudo ufw allow 2302:2345/udp
```

## Running the Server

Now, to run the game, we'll create a small script so we can easily save the addons that we want to run and the configs that we want to load. Inside the *Arma 3* server directory, create the script file (the filename with whatever you want):

``` text
nano start_<name>.sh
```

Inside the editor, paste the following:

``` bash
#!/bin/bash
./arma3server -name=<server_name> -config=<config_file> -mod=@mod1\;@mod2\;@mod3...
```

!!!note
    *server_name* refers to the server name that you used for the *Arma 3 Profile* that is located in **~/.local/share/Arma 3 - Other Profiles**, the *config_file* refers to the *Arma 3 Config* that is inside the *Arma 3* directory, replace them with your own settings.

An example for this script that runs ACE and CBA_A3.

``` bash
#!/bin/bash
./arma3server -name=server -config=modded.cfg -mod=@CBA_A3\;@ACE
```

As per always, the script must be made executable:

``` text
chmod +x <script_name>.sh
```
