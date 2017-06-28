## Maintaining RAID devices with `mdadm`

**Unmount the file systems before doing the actions below**

###### Create a RAID device
```console
host:~# mdadm --assemble --run /dev/md2
```
###### Show all available RAID devices
```console
host:~# cat /proc/mdstat
```
###### Remove the partition from the RAID device
```console
host:~# mdadm /dev/md2 -r /dev/sda4
```
###### Add a partition to the RAID device
```console
host:~# mdadm --zero-superblock /dev/sda4
host:~# mdadm /dev/md2 -a /dev/sda4
```
###### Check the file system on the RAID device
```console
host:~# fsck -y /dev/md2
```
