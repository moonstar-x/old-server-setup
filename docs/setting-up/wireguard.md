# Wireguard

Since the server was recently put under an ISP with a CG-NAT, the easiest way to keep the services accessible from the Internet would be using a VPN such as **Wireguard** through a VPS with a static public IP.

In this particular case, an Ubuntu DigitalOcean droplet with the cheapest options has been chosen.

## Installation

We'll need **Wireguard** on both the VPS and the server. Install them on both computers with the following command:

``` text
sudo apt-get install wireguard
```

## VPS Configuration

Inside the VPS, we'll first generate some keys:

``` text
(umask 077 && printf "[Interface]\nPrivateKey= " | sudo tee /etc/wireguard/wg0.conf > /dev/null)
wg genkey | sudo tee -a /etc/wireguard/wg0.conf | wg pubkey | sudo tee /etc/wireguard/publickey
```

Once this is setup, a config file will be generated in `/etc/wireguard/wg0.conf` with the private key already set. The public key will also be found inside `/etc/wireguard/publickey`.

Open up the `/etc/wireguard/wg0.conf` file and add the following configuration:

``` text
[Interface]
PrivateKey = <PRIVATE KEY>
ListenPort = 55107
Address = 192.168.4.1
PostUp = iptables -A FORWARD -i wg0 -j ACCEPT; iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
PostDown = iptables -D FORWARD -i wg0 -j ACCEPT; iptables -t nat -D POSTROUTING -o eth0 -j MASQUERADE
[Peer]
PublicKey = <SERVER PUBLIC KEY>
AllowedIPs = 192.168.4.2/32
```

!!! note
    You may still not have the server's public key, so complete the [Server Configuration](#server-configuration) first before finalizing with this configuration file.

Now, it is necessary to enable IPV4 Forwarding, for this open up `/etc/sysctl.conf` and uncomment the following line by removing the **#**:

``` text
#net.ipv4.ip_forward=1
```

And then apply these changes by running:

``` text
sudo sysctl -p
sudo sysctl --system
```

Then, we'll start and enable the tunnel service.

``` text
sudo systemctl start wg-quick@wg0
sudo systemctl enable wg-quick@wg0
```

## Server Configuration

Just like we did on the VPS, we need to generate keys on the server.

``` text
(umask 077 && printf "[Interface]\nPrivateKey= " | sudo tee /etc/wireguard/wg0.conf > /dev/null)
wg genkey | sudo tee -a /etc/wireguard/wg0.conf | wg pubkey | sudo tee /etc/wireguard/publickey
```

Once again, a config file and a `publickey` will be found in `/etc/wireguard/`. Open up the `/etc/wireguard/wg0.conf` file and add the following configuration:

``` text
[Interface]
PrivateKey = <PRIVATE KEY>
Address = 192.168.4.2
[Peer]
PublicKey = <VPS PUBLIC KEY>
AllowedIPs = 0.0.0.0/0, ::/0
Endpoint = <VPS IPV4>:55107
PersistentKeepalive = 25
```

Then, we'll start and enable the tunnel service.

``` text
sudo systemctl start wg-quick@wg0
sudo systemctl enable wg-quick@wg0
```

## Testing

To see if everything is fine, try to ping the server from the VPS with:

``` text
ping 192.168.4.2
```

Do the same from the server to the VPS with:

``` text
ping 192.168.4.1
```

If everything is fine, both machines should be able to ping each other.

## IPTables

Once the tunnel is setup, we'll need to configure the VPS's firewall.

### Initialization

By default, we'll setup `iptables` to drop all traffic so run the following:

``` text
sudo iptables -P FORWARD DROP
```

We'll need to allow traffic between `wg0` and `eth0` (assuming that `eth0` is your VPS's network interface).

``` text
sudo iptables -A FORWARD -i wg0 -o eth0 -m conntrack --cstate ESTABLISHED,RELATED -j ACCEPT
sudo iptables -A FORWARD -i wg0 -o eth0 -m conntrack --cstate ESTABLISHED,RELATED -j ACCEPT
```

### Port Forwarding

To forward a port, run the following commands:

``` text
iptables -A FORWARD -i eth0 -o wg0 -p tcp --syn --dport [PORT] -m conntrack --ctstate NEW -j ACCEPT
iptables -t nat -A PREROUTING -p tcp -i eth0 --dport [PORT] -j DNAT --to-destination 192.168.4.2
```

If the you need to forward a port range, just replace `[PORT]` with `INITIAL_PORT:FINAL_PORT`. You may also replace `tcp` with `udp` to forward UDP ports.

!!! note
    Keep in mind that this should be done for every port you need to forward to the server.

### Persistent IPTables

By default, these commands will be gone the next time the VPS reboots. In order to keep the IPTables, run the following commands:

``` text
sudo apt-get install netfilter-persistent
sudo netfilter-persistent save
sudo systemctl enable netfilter-persistent
sudo apt-get install iptables-persistent
```

!!! note
    This should be run every time there's an update to the IPTables rules.
