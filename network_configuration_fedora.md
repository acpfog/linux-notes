
#### Manual network configuration in Fedora for enp0s3 network interface  

Set an IP address and netmask in `/etc/sysconfig/network-scripts/ifcfg-enp0s3`

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

```shell
$ sudo systemctl restart network
```
