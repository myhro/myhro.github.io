---
date: 2018-09-15
layout: post
title: Quick and easy VPNs with WireGuard
...

WireGuard is the new kid on the block in the world of VPNs. It has been receiving a lot of attention lately, especially after [Linus Torvalds himself praised the project][torvalds] last month, resulting in [in-depth guides about its characteristics][ars] being published. The problem is that [practical guides about its setup][linode-guide], including the [official one][official-guide], doesn't show how quick and easy it is to do that. They are full of lengthy, complex and unneeded commands, when everything that is needed are simple configuration files.

This guide won't describe how to actually install WireGuard, as this is [thoroughly covered by the official documentation][install-guide] for every supported platform. It consists of a [loadable kernel module][wg-module] that allows virtual WireGuard network interfaces to be created. In here, an EC2 instance located in Ireland and a virtual machine (based on Vagrant/VirtualBox) in Germany, both running Ubuntu, will be connected.

The first step is to generate a pair of keys for every machine. WireGuard authentication system doesn't rely passwords or certificates that includes hard-to-maintain Certification Authorities (CAs). Everything is done using private/public keys, like [SSH authentication][ssh-auth]:

```
$ wg genkey | tee privatekey | wg pubkey > publickey
$ ls -lh
total 8.0K
-rw-rw-r-- 1 ubuntu ubuntu 45 Sep 15 14:31 privatekey
-rw-rw-r-- 1 ubuntu ubuntu 45 Sep 15 14:31 publickey
```

In the server, the `/etc/wireguard/wg0.conf` configuration file will look like:

```
[Interface]
PrivateKey = 4MtNd3vq/Zb5tc8VgoigLyuONWoCQmnzLKFNuSYLiFY=
Address = 192.168.255.1/24
ListenPort = 51820
PostUp = iptables -A FORWARD -i wg0 -j ACCEPT; iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE; sysctl net.ipv4.ip_forward=1
PostDown = iptables -D FORWARD -i wg0 -j ACCEPT; iptables -t nat -D POSTROUTING -o eth0 -j MASQUERADE; sysctl net.ipv4.ip_forward=0

[Peer]
PublicKey = 0+/w1i901TEFRmEcUECqWab/nwmq0dZLehMzSOKUo04=
AllowedIPs = 192.168.255.2/32
```

Here's an explanation of its fields:

- `PrivateKey` is the server private key. It proves that the server is who it says it is, and the same will be valid for the clients on the other end. One will be able to validate the identity of the other.
- `Address` is the IP and network mask for the VPN network.
- `51820` UDP port which the server will listen for connections.
- `PostUp` are firewall rules and system commands that are needed for the server to act as a gateway, forwarding all network traffic. `PostDown` will disable them when the VPN is deactivated. `eth0` is the name of the main network interface, which can be something different like `ens5` if [systemd's Predictable Network Interface Names][systemd-net] are being used.
- `PublicKey` and `AllowedIPs` defines which peers can connect to this server through a combination of IPs/key pairs. It's important to notice that the IPs defined here are within the VPN network range. Those are not the actual IPs which the client will use to connect to the server over the internet.

The client will also have a `/etc/wireguard/wg0.conf` configuration file, but it will be a little bit different:

```
[Interface]
PrivateKey = yDZjYQwYdsgDmySbUcR0X7b+rdwfZ91rFYxz6m/NT08=
Address = 192.168.255.2/24

[Peer]
PublicKey = e1HJ0ed/lUmCDRUGjCwFZ9Qm2Lt14jNE77TKXyIS1yk=
AllowedIPs = 0.0.0.0/0
Endpoint = ec2-34-253-52-138.eu-west-1.compute.amazonaws.com:51820
```

The `PrivateKey` and `Address` fields here have the same meaning as in the server. The difference is that the `Interface` section won't contain the server parts, like the listening ports and firewall commands. The `Peer` section contains the following fields:

- `PublicKey` is the public key of the server which the client will connect to.
- `AllowedIPs` is interesting here. It means the networks in which their traffic will be forwarded to the server. `0.0.0.0/0` means that all traffic, including connections that goes to the internet, will use the server as a gateway. Using the same VPN network, like `192.168.255.0/24`, would create a P2P VPN, where the client and server will be able to reach each other, but any other traffic (e.g. to the internet) wouldn't be forwarded through this connection.
- `Endpoint` is the hostname or IP and port which the client will use to reach the server in order to establish the VPN connection.

With both machines configured, the VPN interface can be enabled on the server:

```
[ubuntu@ip-172-31-14-254:~]$ sudo wg-quick up wg0
[#] ip link add wg0 type wireguard
[#] wg setconf wg0 /dev/fd/63
[#] ip address add 192.168.255.1/24 dev wg0
[#] ip link set mtu 8921 dev wg0
[#] ip link set wg0 up
[#] iptables -A FORWARD -i wg0 -j ACCEPT; iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE; sysctl net.ipv4.ip_forward=1
net.ipv4.ip_forward = 1
```

If a message like the following is shown in this step:

    Warning: `/etc/wireguard/wg0.conf' is world accessible

This means that the configuration file permissions are too broad - and they shouldn't, as there's a private key in there. This can be fixed with `sudo chmod 600 /etc/wireguard/wg0.conf`.

The command `sudo wg` shows the VPN status:

```
[ubuntu@ip-172-31-14-254:~]$ sudo wg
interface: wg0
  public key: e1HJ0ed/lUmCDRUGjCwFZ9Qm2Lt14jNE77TKXyIS1yk=
  private key: (hidden)
  listening port: 51820

peer: 0+/w1i901TEFRmEcUECqWab/nwmq0dZLehMzSOKUo04=
  allowed ips: 192.168.255.2/32
```

From this point, the VPN can be enabled on the client using the same commands:

```
[vagrant@ubuntu-xenial:~]$ sudo wg-quick up wg0
[#] ip link add wg0 type wireguard
[#] wg setconf wg0 /dev/fd/63
[#] ip address add 192.168.255.2/24 dev wg0
[#] ip link set mtu 1420 dev wg0
[#] ip link set wg0 up
[#] wg set wg0 fwmark 51820
[#] ip -4 route add 0.0.0.0/0 dev wg0 table 51820
[#] ip -4 rule add not fwmark 51820 table 51820
[#] ip -4 rule add table main suppress_prefixlength 0
[vagrant@ubuntu-xenial:~]$ sudo wg
interface: wg0
  public key: 0+/w1i901TEFRmEcUECqWab/nwmq0dZLehMzSOKUo04=
  private key: (hidden)
  listening port: 47603
  fwmark: 0xca6c

peer: e1HJ0ed/lUmCDRUGjCwFZ9Qm2Lt14jNE77TKXyIS1yk=
  endpoint: 34.253.52.138:51820
  allowed ips: 0.0.0.0/0
  latest handshake: 1 second ago
  transfer: 92 B received, 292 B sent
[vagrant@ubuntu-xenial:~]$ curl https://myhro.info/ip
curl/7.47.0

34.253.52.138

IE
```

The most impressive part is how quickly the connection is done. There are no multi-second handshakes like any other VPN solution and it can be used instantly. After playing with it, it becomes easier to understand why WireGuard is attracting so much attention. Specially because it's so unobtrusive that one can use it without even realizing it's turned on.


[ars]: https://arstechnica.com/gadgets/2018/08/wireguard-vpn-review-fast-connections-amaze-but-windows-support-needs-to-happen/
[install-guide]: https://www.wireguard.com/install/
[linode-guide]: https://www.linode.com/docs/networking/vpn/set-up-wireguard-vpn-on-ubuntu/
[official-guide]: https://www.wireguard.com/quickstart/
[ssh-auth]: /2017/01/how-ssh-authentication-works
[systemd-net]: https://www.freedesktop.org/wiki/Software/systemd/PredictableNetworkInterfaceNames/
[torvalds]: http://lkml.iu.edu/hypermail/linux/kernel/1808.0/02472.html
[wg-module]: http://lkml.iu.edu/hypermail/linux/kernel/1809.1/05548.html
