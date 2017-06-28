
## Some useful commands for HDD maintenance

###### Show all block devices
```console
host:~# blkid
```
###### Get a list of UUID
```console
host:~# ls -l /dev/disk/by-uuid
```
###### Determine the file system type
```console
host:~# blkid /dev/sda4
```
###### Check the file system
```console
host:~# umount /dev/sda4
host:~# fsck -y /dev/sda4
```
###### Show SMART information of the disk
```console
host:~# smartctl -a /dev/sda
```
###### Check the surface of the disk
```console
host:~# badblocks -v /dev/sda
```
######  Brief information about the HDD
```console
host:~# hdparm -i /dev/sda
```
######  Detail information about the HDD
```console
host:~# hdparm -I /dev/sda
```
###### Change the volume label
```console
host:~# tune2fs -L Newname /dev/sda7
```
###### Create or change partitions
```console
host:~# cfdisk /dev/sda
```
###### Prepare a swap partition
```console
host:~# mkswap /dev/sda1
```
