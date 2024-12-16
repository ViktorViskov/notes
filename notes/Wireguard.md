# Configs
## Server
```
[Interface]
Address = 10.0.1.1/32
ListenPort = 55830
PrivateKey = serv_privkey
PostUp = iptables -A FORWARD -i %i -j ACCEPT; iptables -A FORWARD -o %i -j ACCEPT; iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
PostDown = iptables -D FORWARD -i %i -j ACCEPT; iptables -D FORWARD -o %i -j ACCEPT; iptables -t nat -D POSTROUTING -o eth0 -j MASQUERADE


[Peer]        #client_1
PublicKey = client_pubkey
AllowedIPs = client_ip
```
## Client
### All traffic
```
[Interface]
PrivateKey = client_privkey
Address = 10.0.1.106/32

[Peer]
PublicKey = serv_pubkey
Endpoint = vpn.viktorviskov.com:55830
#AllowedIPs = 192.168.0.0/24, 10.0.1.0/24
AllowedIPs = 0.0.0.0/0
PersistentKeepalive = 120
```
### Specific traffic
```
[Interface]
PrivateKey = client_privkey
Address = 10.0.1.106/32

[Peer]
PublicKey = serv_pubkey
Endpoint = vpn.viktorviskov.com:55830
AllowedIPs = 192.168.0.0/24, 10.0.1.0/24
#AllowedIPs = 0.0.0.0/0
PersistentKeepalive = 120

```
# Commands
Status
```sh
sudo wg
```

Enable
```sh
sudo wg-quick up home
```

Disable
```sh
sudo wg-quick down home
```