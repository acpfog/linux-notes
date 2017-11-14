
## How to manually configure a network interface in Fedora

Set an IP address and netmask in `/etc/sysconfig/network-scripts/ifcfg-enp0s3`  
(replace `enp0s3` if your network interface has another name)

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
(replace `ens3` if your network interface has another name)

```text
auto ens3
iface ens3 inet static
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
$ sudo ip addr flush dev ens3 && sudo systemctl restart networking.service
```
