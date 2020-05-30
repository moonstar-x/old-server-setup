# Minecraft

## Installing Requirements

The following installs a vanilla **Minecraft** server, if you're looking for a modded server you'll have to look up *Forge* or *CraftBukkit* guides. **Minecraft** as we know requires Oracle Java in order to run. Ubuntu does not come with Java installed nor is it available on the default package repos, so we'll need to add a PPA repo and install it from there, just do the following:

    sudo apt-add-repository ppa:webupd8team/java
    sudo apt-get update
    sudo apt-get install oracle-java8-installer

## Installation

After installing Oracle Java, we're ready to install a **Minecraft** server. We'll change to our *steam* user since it's dedicated to our game servers. We'll also create a folder specifically for it.

    sudo -iu steam
    cd ~
    mkdir minecraft && cd minecraft

Head over to the [official Minecraft website](https://minecraft.net/en-us/download/server) to download the newest **Minecraft** server executable and start it up (the example below may be outdated).

    wget https://launcher.mojang.com/mc/game/1.13/server/d0caafb8438ebd206f99930cfaecfa6c9a13dca0/server.jar
    java -Xms2G -Xmx2G -jar server.jar nogui

!!! note ""
     Replace the first two arguments with the amount of RAM you want to dedicate for the Minecraft server.

If everything went well, the server closed itself and a little *eula.txt* file was generated in the server's directory. We need to edit that file to accept the EULA agreement.

    nano eula.txt

Change the last line to:

    eula=true

Now fire up the server again and stop it once it finishes loading up.

    java -Xms2G -Xmx2G -jar server.jar nogui
    stop

## Configuration

Now that all the required files have been generated, we can continue to set up our server's configuration.

    nano server.properties

All the settings here are pretty much self explanatory but there's one that I'm going to address and it's *online-mode*. This setting tells the **Minecraft** server whether it should connect to the Minecraft.net servers to check if all the users connecting have a **Minecraft** Premium account and download their respective skins. This option is generally set to `true`, but if we have friends that do not have **Minecraft** Premium they'll be able to join our server if we set `online-mode=false`, keep in mind that everyone's skins will be set to default and anyone that joins through a 3rd-party launcher may be able to change their name anytime (risking identity theft or ban avoidance).

Remember, we need to allow connections through the firewall (you may need to change to an user that has access to the *sudo* command, i.e you main administrator user):

    sudo ufw allow 25565/tcp

We'll now create a script to run the server a lot easier. Create a file running:

    nano start.sh

Paste the following:

    #!/bin/bash
    java -Xms2G -Xmx2G -jar server.jar nogui

Make it executable:

    chmod +x start.sh

## Running the Server

You can now start the server just by running the start script.

    ./start.sh
