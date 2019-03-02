# Screeps

## Installing Requirements

The following game server requires **Node.js**, in order to install it, we'll run the following commands:

    curl -sL https://deb.nodesource.com/setup_11.x | sudo -E bash -
    sudo apt-get install nodejs

Apart from **Node.js** we will also need the basic **Build Essentials** and **Python**.

    sudo apt-get install build-essential python

Some changes were done in the way that *npm* works which broke this package alongside others. Luckily, you can install this with *yarn* which can fully replace *npm*. To install this, we'll need to configure the repository:

    curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add -
    echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list

Then, we update our repositories and install *yarn*.

    sudo apt-get update && sudo apt-get install yarn

## Installation

To install *Screeps*, we will switch to our *steam* user where we store our game servers and we'll create a folder in our home directory.

    sudo -i -u steam
    mkdir screeps && cd screeps

Here we can install *Screeps*.

    yarn add screeps

Before starting the server, we will need a *Steam Web API Key*, head over to [this link](https://steamcommunity.com/dev/apikey), copy it to your clipboard and paste it when it's required.

    npx screeps init

After doing this you can now start the server.

    npx screeps start

As per usual, we will need to add some firewall rules (as a sudoer):

    sudo ufw allow 21025/tcp