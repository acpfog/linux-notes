
#### Manual network configuration in Fedora for enp0s3 network interface  

In `/etc/sysconfig/network-scripts/ifcfg-enp0s3` set an IP address and netmask

```console
IPADDR=192.168.0.2
NETMASK=255.255.255.0
```

Put a line with the default gateway IP address to `/etc/sysconfig/network`

```console
GATEWAY=192.168.0.1
```

Append a line with a DNS server address to `/etc/resolv.conf`

```console
nameserver 192.168.0.1
```

Apply changes

```bash
# systemctl restart network
```
