# OpenVPN

This section of the guide will focus on installing a VPN service in the server. This process can be a little bit tedious and even complicated, it is all explained here step by step.

## Installation

We'll install two packages and make a folder.

    sudo apt-get install openvpn easy-rsa
    make-cadir ~/openvpn-ca
    cd ~/openvpn-ca

## Setting Up and Building Certificate Authority

We'll change the certificate authority variables.

    nano vars

Change the following information according to your needs, I recommend you leaving **KEY_NAME** as *"server"*.

    export KEY_COUNTRY="US"
    export KEY_PROVINCE="CA"
    export KEY_CITY="SanFrancisco"
    export KEY_ORG="Fort-Funston"
    export KEY_EMAIL="me@myhost.mydomain"
    export KEY_OU="MyOrganizationalUnit"
    export KEY_NAME="server"

We source the variables now and clean our environment.

    source vars
    ./clean-all
    ln -s openssl-1.0.0.cnf openssl.cnf

We can now build our certificate authority (hit enter for everything, leave any challenge password blank).

    ./build-ca

## Building Server Certificate, Key and Encryption

We now need to generate our server and Diffie-Hellman keys and our HMAC signature (leave blank when asked for challenge password).

    ./build-key-server server BLANK PASS
    ./build-dh
    openvpn --genkey --secret keys/ta.key

## Configuring the VPN Server

We'll transfer the server keys and certificates generated previously to `/etc/openvpn`.

    cd keys
    sudo cp ca.crt server.crt server.key ta.key dh2048.pem /etc/openvpn

Now we'll create a server configuration file, you can look up for server configurations, base yourself from the default one or just copy my own. If you want to base yourself from the default one, run:

    gunzip -c /usr/share/doc/openvpn/examples/sample-config-files/server.conf.gz | sudo tee /etc/openvpn/server.conf

Since I already know what settings I'm going to need for the VPN server, I'll directly create the server configuration.

    sudo nano /etc/openvpn/server.conf

My own configuration goes as follows:

    local 0.0.0.0
    port 1194
    proto udp
    dev tun
    ca ca.crt
    cert server.crt
    key server.key
    dh dh2048.pem

    server 10.8.0.0 255.255.255.0
    ifconfig-pool-persist ipp.txt
    keepalive 10 60
    cipher AES-256-CBC
    comp-lzo
    max-clients 20
    persist-key
    persist-tun
    status openvpn-status.log
    verb 3
    client-to-client

    tls-auth ta.key
    key-direction 0
    auth SHA256

    user nobody
    group nogroup

    # ROUTE
    push "remote-gateway def1"
    push "dhcp-option DNS 8.8.8.8"
    push "dhcp-option DNS 8.8.4.4"

## Allow IP Forwarding

In order for the server to forward inbound VPN connections to the main network interface the server uses, we'll need to set up IP forwarding. Access the following file:

    sudo nano /etc/sysctl.conf

Uncomment the following line:

    net.ipv4.ip_forward=1

To see if changes were made, run:

    sudo sysctl -p

Now, we need to know what is our main network interface, you can use:

    ifconfig

You'll receive a similar output:

    enp0s3: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
            inet 192.168.0.15  netmask 255.255.255.0  broadcast 192.168.0.255
            inet6 ::a00:27ff:fe49:8561  prefixlen 64  scopeid 0x0<global>
            inet6 fe80::a00:27ff:fe49:8561  prefixlen 64  scopeid 0x20<link>
            ether 08:00:27:49:85:61  txqueuelen 1000  (Ethernet)
            RX packets 755025  bytes 1132393073 (1.1 GB)
            RX errors 0  dropped 0  overruns 0  frame 0
            TX packets 94785  bytes 9330879 (9.3 MB)
            TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

The network interface is the first word you see in the output (in this case *enp0s3*), save it for later because we'll need it. Edit the UFW rules from the config file:

    sudo nano /etc/ufw/before.rules

Inside this file you'll see the following:

    #
    # rules.before
    #
    # Rules that should be run before the ufw command line added rules. Custom
    # rules should be added to one of these chains:
    #   ufw-before-input
    #   ufw-before-output
    #   ufw-before-forward
    #

Just after these lines we'll add the following:

    # START OPENVPN RULES
    # NAT table rules
    *nat
    :POSTROUTING ACCEPT [0:0]
    # Allow traffic from OpenVPN client to enp0s3 (change to the interface you discovered!)
    -A POSTROUTING -s 10.8.0.0/8 -o enp0s3 -j MASQUERADE
    COMMIT
    # END OPENVPN RULES

We also need to change UFW's configuration.

    sudo nano /etc/default/ufw

Here we'll replace `DEFAULT_FORWARD_POLICY="DROP"` to `DEFAULT_FORWARD_POLICY="ACCEPT"` and save the file.

Now to add the firewall rules to enable connections to the VPN and allow any connection through the VPN:

    sudo ufw allow 1194/udp
    sudo ufw allow in on tun0
    sudo ufw allow out on tun0

## Starting the Server

The server is ready to start (make sure to replace server with whatever server name you used when creating the server keys):

    sudo systemctl start openvpn@server

If there was no issues you'll be able to see a new interface called `tun0` when using `ifconfig`. If that isn't the case then most likely your server configuration is causing issues.

To enable the service so the server auto-starts on login:

    sudo systemctl enable openvpn@server

## Creating a Client Base Configuration

The server isn't ready yet, we still need to create client keys, certificates and profiles. To make this job a lot easier we'll create a base configuration which is going to serve as the base for all clients generated and we'll create a client generating script. For the base configuration we'll create some folders and the base configuration itself.

    mkdir -p ~/client-configs/files
    chmod 700 ~/client-configs/files
    cp /usr/share/doc/openvpn/examples/sample-config-files/client.conf ~/client-configs/base.conf
    nano ~/client-configs/base.conf

In the text editor you'll see that a configuration is already there, we'll need to make some changes in there to suit our needs.

* Firstly, look for `remote server_IP_address 1194` and change `server_IP_address` to your server's external IP address, you can add your numerical IP address (xxx.xxx.xxx.xxx) only if it's static, if that's not your case I recommend you checking out the following: [NO-IP DUC](https://moonstar-x.github.io/server-setup/services/#no-ip-duc).
* We'll not uncomment the following items: `user nobody`, `group nogroup` and `comp-lzo`.
* Now we need to comment the following items: `ca ca.crt`, `cert client.crt` and `key client.key`.
* We need to add the following: `cipher AES-256-CBC`, `auth SHA256` and `key-direction 1`.

Save and quit the file.

## Client Generating Script

Create a script in the `~/client-configs` folder:

    nano ~/client-configs/make_config.sh

Paste the following in the text editor:

    #!/bin/bash

    # First argument: Client identifier

    KEY_DIR=~/openvpn-ca/keys
    OUTPUT_DIR=~/client-configs/files
    BASE_CONFIG=~/client-configs/base.conf

    cat ${BASE_CONFIG} \
        <(echo -e '<ca>') \
        ${KEY_DIR}/ca.crt \
        <(echo -e '</ca>\n<cert>') \
        ${KEY_DIR}/${1}.crt \
        <(echo -e '</cert>\n<key>') \
        ${KEY_DIR}/${1}.key \
        <(echo -e '</key>\n<tls-auth>') \
        ${KEY_DIR}/ta.key \
        <(echo -e '</tls-auth>') \
        > ${OUTPUT_DIR}/${1}.ovpn

To make the following script executable:

    chmod 700 ~/client-configs/make_config.sh

## Creating Keys, Certificates and Profiles for Clients

You'll need to repeat this process for every client that will use the VPN, client profiles are not interchangeable, meaning that each client needs to use it's own generated profile and set of keys, trying to use a single profile for multiple devices will result in issues when connecting to the VPN. We'll go to the `~/openvpn-ca` folder where all our keys are saved and source our variables.

    cd ~/openvpn-ca
    source vars

For every client, run the following, make sure to change **$CLIENT** with the client name and remember to set a blank challenge password:

    ./build-key $CLIENT

Once we've generated all the client keys and certificates we need, we'll go back to the folder where our script is located and run it for every client we made.

    cd ~/client-configs
    ./make_config.sh $CLIENT

Our client profiles will be saved in the `./files` folder.

## Tunnel all traffic through the VPN

My VPN configuration requires me to have two profiles, one where I can connect to my self-hosted services through a VPN and one where I can route all my traffic through the VPN. To route all the traffic to the VPN, simply copy your client profile and add the following line to the config:

    redirect-gateway def1

## Connecting to the VPN

To connect to the VPN your client device will need [OpenVPN](https://openvpn.net/) installed, you can find it for almost any platform. You'll need to transfer from the server to the client device their respective profile: `~/client-configs/files/$CLIENT.ovpn` and the **ta.key** file `~/openvpn-ca/keys/ta.key` (this seems to be only required on mobile clients but chances are your desktop client may need it as well). When connecting to the VPN, the client will receive it's own virtual private IP (10.8.0.x), the server's IP is generally (10.8.0.1). You can now establish connections to the server that are not port forwarded by the router (Samba/CIFS for example).