# RTMP Simulcast

If you like to stream your video games to multiple platforms simultaneously, then *RTMP Simulcast* is for you. There are services out there that let you do this for free, but they have some cons:

* Depending on the service you use, your stream titles or descriptions could be used for advertising purposes, which basically means you lose complete control of what your stream description displays to your viewers.
* Since these services are used by multiple people at the same time, streaming platforms will most likely detect a huge pool of users streaming from the same IP which means that you'll be targeted by a low exposure algorithm that will, ironically, make your stream harder to find.

You can bypass these problems by streaming simultaneously to your desired platforms yourself, but keep in mind, there are some difficulties as well:

* Doing this (ideally) requires you to have a second computer (performance doesn't matter too much, you could even use a Raspberry PI for this), you could technically achieve the same result with a virtual machine inside the streaming computer but it would represent a huge performance drop.
* Since you would be streaming to multiple platforms yourself, you would need a much higher upload bandwidth (around the stream bitrate multiplied by the number of platforms you're streaming to). For instance, my stream gets rendered at a bitrate of 6000 *kbps*, if I wanted to stream to 3 different platforms at the same time, I would need around 20000 *kbps*.

## Installation

To achieve this, we will need **nginx**, to install it, run:

``` text
sudo apt-get install nginx libnginx-mod-rtmp
```

## Configuration

Now, we need to edit the **nginx** configuration file.

``` text
sudo nano /etc/nginx/nginx.conf
```

At the end of the file, append the following lines:

``` text
# RTMP Stream Simulcast
rtmp {
    server {
        listen 1935;

        application live {
            live on;
            record off;
            push rtmp://ingest.server.com/application/stream_key
        }

        application local {
            live on;
            record off;
        }
    }
}
```

!!! note
  Replace `rtmp://ingest.sever.com/application/stream_key` with the actual ingest server of the 	platform you want to stream to. For example: `rtmp://a.rtmp.youtube.com/live2/{my_stream_key}`. You can push multiple links.

!!! note
  If your machine is running another web server such as **Apache**, comment out or delete all the directives inside the **http{}** group.

Make sure to open the *RTMP* port:

``` text
sudo ufw allow 1935
```

## Streaming Settings

In your streaming software, change your streaming server to the following:

``` text
rtmp://server_local_ip/live
```

And you can place whatever you want as the stream key.

For testing purposes, you can also stream to `rtmp://server_local_ip/local` and watch it to see how it performs.

## Watching Your Stream Locally

If you wish to see how your server perceives your stream, you can use a *RTMP* player (VLC will do the trick), and open the stream at the url `rtmp://server_local_ip/local/{stream_key}`. Replace `{stream_key}` with the actual streaming key that you're currently using.
