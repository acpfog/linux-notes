## Working with the Linux kernel modules

###### List all modules loaded in the kernel
```console
host:~# lsmod
```
###### Load a module
```console
host:~# modprobe ip_conntrack_ftp
```
###### Load a module at boot time
```console
host:~# echo "ip_conntrack_ftp" >> /etc/modules
```
