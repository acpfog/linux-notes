
## How to manually configure a network interface in Fedora

Set an IP address and netmask in `/etc/sysconfig/network-scripts/ifcfg-eth0`  
(replace `eth0` if your network interface has another name)

```text
IPADDR=192.168.0.2
NETMASK=255.255.255.0
```

Put a line with the default gateway IP address to `/etc/sysconfig/network`

```text
GATEWAY=192.168.0.1
```

Append a line with the DNS server IP address to `/etc/resolv.conf`

```text
nameserver 192.168.0.1
```

Apply changes

```console
$ sudo systemctl restart network
```

## How to manually configure a network interface in Ubuntu

Set an IP address, netmask and gateway for your interface in `/etc/network/interfaces`
(replace `eth0` if your network interface has another name)

```text
auto eth0
iface eth0 inet static
    address 192.168.0.2
    netmask 255.255.255.0
    gateway 192.168.0.1
    dns-nameservers 192.168.0.1
    dns-search contactis.local
```

Set networks addresses in `/etc/networks`

```text
default     0.0.0.0
loopback    127.0.0.0
link-local  169.254.0.0
localnet    192.168.0.0
```

Apply changes

```console
$ sudo ifdown eth0
$ sudo ifup eth0
```

`dhclient` process doesn't stop when you change the interface configuration from `dhcp` to `static`.  
You can stop it by command

```console
$ sudo ps ax | grep [d]hclient | grep eth0 | awk '{print $1}' | xargs kill
```

You can use the networking init script for apply changes

```console
$ sudo systemctl restart networking.service
```

or

```console
$ sudo service networking restart
```

But if the interface already has an IP address it may brings an error

```text
Failed to start Raise network interface
```

Delete an assigned IP address before restarting the network service.

```console
$ sudo ip addr flush dev eth0
$ sudo systemctl restart networking.service
```

