# Port Forwarding
Here's a table that contains the required default ports that need to be forwarded on the router (and allowed by the firewall) so the services are accessible from outside the private network. Ports can change if desired, port forwarding rules should be set up accordingly.

## Port Forwarding Table

| Service                     | Port Range  | Protocol |
|-----------------------------|-------------|----------|
| SSH                         | 22          | TCP      |
| Virtualbox - RDP            | 3389        | TCP      |
| TeamSpeak 3 - Voice         | 9987        | UDP      |
| TeamSpeak 3 - File Transfer | 30033       | TCP      |
| TeamSpeak 3 - ServerQuery   | 10011       | TCP      |
| OpenVPN                     | 1194        | UDP      |
| Squid                       | 1337        | TCP      |
| Plex                        | 32400       | TCP      |
| Transmission                | 9091        | TCP      |
| Source Games                | 27000-27050 | TCP/UDP  |
| Arma 3                      | 2302-2345   | TCP/UDP  |
| Assetto Corsa               | 9600        | TCP/UDP  |
| Assetto Corsa - HTTP        | 8081        | TCP      |
| Minecraft                   | 25565       | TCP      |

If you don't know what internal IP the server is running on you can always run:

    ifconfig
