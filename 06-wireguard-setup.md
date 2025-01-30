# Wireguard multipeer Configuration

## Prerequisits

### Enabling forwarding

To make possible communicate two peers connected to a peer acting as vpn server, the server must enable packet forward changing the file:

/etc/sysctl.conf

Uncomment the line with

net.ipv4.ip_forward=1

save and run to update configuration

$ sysctl -p


### Install Wireguard
Install WireGuard via whatever package manager you use.  For me, I use apt.

```bash
$ sudo add-apt-repository ppa:wireguard/wireguard
$ sudo apt-get update
$ sudo apt-get install wireguard
```

Generate key your key pairs.  The key pairs are just that, key pairs.  They can be
generated on any device, as long as you keep the private key on the source and 
place the public on the destination.  



From the https://wiki.archlinux.org/title/WireGuard, use (not tested, commented by @Roy-Orbison)
```bash
wg genkey | (umask 077 && tee privatekey) | wg pubkey > publickey

So the private key is created not readable to others.
```

```bash
$ wg genkey | tee privatekey | wg pubkey > publickey
```

One can also generate a preshared key to add an additional layer of symmetric-key cryptography to be mixed into the already existing public-key cryptography, for post-quantum resistance.

```bash
$ wg genpsk > preshared
```

Take the above private key, and place it in the server.  And conversely, put the 
public key on the peer.  Generate a second key pair, and do the opposite, put the
public on the server and the private on the peer.  Put the preshared key in the client config if you choose to use it.

## Server configuration

On the server, create a conf file - /etc/wireguard/wg0.conf (These are examples,
so use whatever IP ranges and CIDR blocks that will work for your network.)
The number after the ip (CIDR (Classless Inter-Domain Routing) notation) e.g. "/24" or "/32" is very important.


| CIDR  | Subnet Mask        | Total IPs | Usable IPs                     | Network Example                  |
|-------|--------------------|-----------|--------------------------------|----------------------------------|
| /32   | 255.255.255.255   | 1         | 0                              | Single host only                |
| /31   | 255.255.255.254   | 2         | 2 (for point-to-point links)   |                                  |
| /30   | 255.255.255.252   | 4         | 2                              | 192.168.0.0 - 192.168.0.3       |
| ...   | ...............   | ..        | ...                            | ...                             |
| /24   | 255.255.255.0     | 256       | 254                            | 192.168.0.0 - 192.168.0.255     |

```bash
[Interface]
Address = [custom IP in the range 10.8.x.x]/24
PostUp = ufw route allow in on wg0 out on [network adapter of the wireguard server]
PostUp = iptables -t nat -I POSTROUTING -o network adapter of the wireguard server] -j MASQUERADE
PreDown = ufw route delete allow in on wg0 out on [your network adapter]
PreDown = iptables -t nat -D POSTROUTING -o [network adapter of the wireguard server] -j MASQUERADE
ListenPort = [port of the wireguard server]
PrivateKey = [private key of the wireguard server]

[Peer]
# Peer 1
PublicKey = [public key of peer 1]
AllowedIPs = [custop IP in the range 10.x.x.x+1]/32

[Peer]
# Peer 2
PublicKey = [public key of the peer 2
AllowedIPs = [custop IP in the range 10.x.x.x+2]/32
```

## Peer 1 configuration
In every peer you install wireguard and create key pair.

```bash
[Interface]
PrivateKey = [private key of peer 1]
Address = [custop IP in the range 10.x.x.x+1]/24
DNS = 1.1.1.1, 8.8.8.8

[Peer]
PublicKey = [public key of the wireguard server (can be checked with `sudo wg` on the server)]
AllowedIPs = 0.0.0.0/0
Endpoint = [public IP of the wireguard server]:[port of the wireguard server]
```

## Peer 2 configuration (OpenWRT)
These are mine configs in file /etc/config/network

```bash
...

config device
        option name 'br-lan'
        option type 'bridge'
        list ports 'eth0'

config interface 'lan'
        option device 'br-lan'
        option proto 'static'
        option ipaddr '[ip of openWRT can be any]'
        option netmask '255.255.255.0'
        option ip6assign '60'
        option force_link '1'

config interface 'wwan'
        option proto 'dhcp'
        option peerdns '0'
        option dns '1.1.1.1 8.8.8.8'

config interface 'vpn'
        option proto 'wireguard'
        option private_key '[private key of peer 2]'
        list addresses '[custop IP in the range 10.x.x.x+2]/24'

config wireguard_vpn 'wgserver'
        option public_key '[public key of wireguard server]'
        option endpoint_host '[public ip of wireguard server]'
        option endpoint_port '[port of the wireguard server]'
        option persistent_keepalive '25'
        option route_allowed_ips '1'
        list allowed_ips '0.0.0.0/0'
```

## Output of `sudo wg`

```bash
$ sudo wg
interface: wg0
  public key: [public key of the wireguard server]
  private key: (hidden)
  listening port: [port of the wireguard server]

peer: [public key of peer 1]
  endpoint: [ip and port of endpoing of peer 1]
  allowed ips: [ip of peer 1]/32
  latest handshake: 10 days, 19 hours, 44 minutes, 18 seconds ago
  transfer: 366.14 MiB received, 321.77 MiB sent

peer: [public key of peer 2]
  endpoint: [ip and port of endpoing of peer 2]
  allowed ips: [ip of peer 2]/32
  latest handshake: 24 days, 17 hours, 23 minutes, 16 seconds ago
  transfer: 104.36 MiB received, 235.48 MiB sent
```

## More about AllowedIPs

```bash
# AllowedIPs: controls which network traffic enters and leaves the client 
# AllowedIPs: acts as a routing table when sending and an Access Control List (ACL) when receiving
# AllowedIPs: for client-to-Internet-VPN scenario, use: 0.0.0.0/0, ::/0
# AllowedIPs: 0.0.0.0/0, ::/0 allows traffic from any source to any target
# AllowedIPs: 0.0.0.0/0, ::/0 allows traffic to and from the entire internet
# AllowedIPs: for client-to-server(s)-VPN scenario, use: server.IP.address, server(s).subnet.CIDR 
# AllowedIPs: client-to-server(s)-VPN scenario example: 172.16.2.1/32, 10.2.1.0/16
# AllowedIPs: 172.16.2.1/32, 10.2.1.0/16 allows traffic to and from a WireGuard server at 172.16.2.1 and any server/device in the 10.2.1.0/16 subnet
# AllowedIPs: /16 is 65,534 available IP address
```
from [dhackney](https://gist.github.com/dhackney)
## Source
https://gist.github.com/chrisswanda/88ade75fc463dcf964c6411d1e9b20f4
