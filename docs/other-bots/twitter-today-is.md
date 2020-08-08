# Twitter Today Is Bot

## Installing Requirements

The Twitter Today Is bot requires [Node.js](https://nodejs.org/) and [git-lfs](https://github.com/git-lfs/git-lfs/wiki/Installation) to properly clone the repo with all the images in the `assets` folder of the repo. You will also need a [Twitter Developer Account](https://developer.twitter.com/en) for the required keys to run this bot properly.

## Installation

To install this bot, we'll clone the project from its repo from the `bots` user:

``` text
sudo -iu bots
git clone https://github.com/moonstar-x/twitter-today-is.git
```

And finally, to install the dependencies, run inside the `twitter-today-is` folder:

``` text
npm install
```

## Configuration

Inside the `twitter-today-is` folder, we need to create an `.env` file with the following variables:

``` bash
TWITTER_CONSUMER_KEY="YOUR APP CONSUMER KEY"
TWITTER_CONSUMER_SECRET="YOUR APP CONSUMER SECRET"
TWITTER_ACCESS_TOKEN="YOUR USER ACCESS KEY"
TWITTER_ACCESS_SECRET="YOUR USER ACCESS SECRET"
TIMEZONE="YOUR TIMEZONE"
COORD_LAT="TWEET COORDINATES LATITUDE AS NUMBER"
COORD_LONG="TWEET COORDINATES LONGITUDE AS NUMBER"
NODE_ENV=production
```

!!! note
  `NODE_ENV=production` needs to be present, otherwise the bot will execute the tweet every minute rather than everyday at 10:00 AM.

After everything has been set up, you can start the bot by running:

``` text
npm start
```

## Auto-starting

To autostart this bot, we'll create a service file. Using a sudoer account, run:

``` text
sudo nano /etc/systemd/system/twitter-today-is.service
```

And inside the editor, paste the following text:

``` bash
[Unit]
Description=Twitter Today Is Robot
After=network.target

[Service]
Type=simple
User=bots
WorkingDirectory=/home/bots/twitter-today-is/
ExecStart=/usr/bin/npm start
Restart=always

[Install]
WantedBy=multi-user.target
```

Now, we refresh, start and enable the service.

``` text
sudo systemctl daemon-reload
sudo systemctl start twitter-today-is.service
sudo systemctl enable twitter-today-is.service
```
