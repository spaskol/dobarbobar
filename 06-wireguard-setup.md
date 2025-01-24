Install WireGuard via whatever package manager you use.  For me, I use apt.

```bash
$ sudo add-apt-repository ppa:wireguard/wireguard
$ sudo apt-get update
$ sudo apt-get install wireguard
```bash

Generate key your key pairs.  The key pairs are just that, key pairs.  They can be
generated on any device, as long as you keep the private key on the source and 
place the public on the destination.  

```bash
$ wg genkey | tee privatekey | wg pubkey > publickey
bash

One can also generate a preshared key to add an additional layer of symmetric-key cryptography to be mixed into the already existing public-key cryptography, for post-quantum resistance.

```bash
# wg genpsk > preshared
bash

Take the above private key, and place it in the server.  And conversely, put the 
public key on the peer.  Generate a second key pair, and do the opposite, put the
public on the server and the private on the peer.  Put the preshared key in the client config if you choose to use it.

## Server configuration

On the server, create a conf file - /etc/wireguard/wg0.conf (These are examples,
so use whatever IP ranges and CIDR blocks that will work for your network.)

[Interface]
Address = [custom IP in the range 10.8.x.x]/24
PostUp = ufw route allow in on wg0 out on [your network adapter]
PostUp = iptables -t nat -I POSTROUTING -o [your network adapter] -j MASQUERADE
PreDown = ufw route delete allow in on wg0 out on [your network adapter]
PreDown = iptables -t nat -D POSTROUTING -o [your network adapter] -j MASQUERADE
ListenPort = [port of the server]
PrivateKey = [private key of the server]

[Peer]
# Peer 1
PublicKey = [public key of peer 1]
AllowedIPs = [custop IP in the range 10.8.x.x+1]/32


[Peer]
# Peer 2
PublicKey = [public key of the peer 2
AllowedIPs = [custop IP in the range 10.8.x.x+2]/32


## Peer 1 configuration
[Interface]
PrivateKey = [private key of peer 1]
Address = [custop IP in the range 10.8.x.x+1]/24
DNS = 1.1.1.1, 8.8.8.8

[Peer]
PublicKey = [public key of the server (can be checked with `sudo wg` on the server)]
AllowedIPs = 0.0.0.0/0
Endpoint = [public IP of the wireguard server]:[port of the server]
