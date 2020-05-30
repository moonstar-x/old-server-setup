# FastDL

As a disclaimer, the servers that appear under this category are all compatible with FastDL for custom content.

FastDL is a way to host your custom content for your game servers, it delivers the custom content that your server requires to run properly to connecting players. To accomplish this, FastDL requires an webserver where the content can be stored and accessed. When multiple users join at the same time it can require a lot of bandwidth, for this reason we will use a free hosting solution instead of self-hosting it ourselves, in this case we will use [000webhhost](https://www.000webhost.com/). 000webhost offers 10GB of bandwidth, 1GB of storage and 400 requests-per-minute for free, which is more than enough for a simple *FastDL* host.

## Setting Up Hosting

Firstly, create an account at [000webhost](https://www.000webhost.com/), make sure you don't use a password that you generally use since this service is fairly unreliable and has endured many security breaches (it's still worth it for something as trivial as *FastDL*).

Then, create a site, give it a name that will be easy for you to remember since it will be your web link (sitename.000webhostapp.com), set again a password for this site that is secure but that you don't use.

## Uploading Content

After that, use an FTP client (Filezilla and WinSCP are very good). Connect to your site with the following info:

* **Server IP**: files.000webhost.com
* **User**: The site name that you chose previously.
* **Password**: The password that you chose previously.
* **Port**: 21

Inside you'll find a folder called `public_html`, access it and create a folder for each of your games. Inside each folder you should create the following folder structure for **Source** games:

``` text
<source_gamename>
│
└───maps
|   |   file.example
|   |   ...
│
└───materials
|   |   file.example
|   |   ...
│
└───models
|   |   file.example
|   |   ...
│
└───resource
|   |   file.example
|   |   ...
│
└───sound
|   |   file.example
|   |   ...
```

For **GoldSrc** games:

``` text
<goldsrc_gamename>
│
└───gfx
|   |   file.example
|   |   ...
│
└───maps
|   |   file.example
|   |   ...
│
└───models
|   |   file.example
|   |   ...
│
└───resource
|   |   file.example
|   |   ...
│
└───sound
|   |   file.example
|   |   ...
│
└───sprites
|   |   file.example
|   |   ...
```

After uploading all your content to your *FTP* server, head over to the *server.cfg* for the corresponding game and add the following lines to it:

``` text
sv_allowdownload 1
sv_allowupload 1
sv_downloadurl "https://<sitename>.000webhostapp.com/<gamename>/"
```

Now every time a player connects to your server and needs to download the required content they'll download it through the *FastDL* instead of the server, putting less strain on the game server this way.
