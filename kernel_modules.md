### Working with the Linux kernel modules

1. List all modules loaded in the kernel
```bash
host:~# lsmod
```
2. Load a module
```bash
host:~# modprobe ip_conntrack_ftp
```
3. Load a module at boot time
```bash
host:~# echo "ip_conntrack_ftp" >> /etc/modules
```
