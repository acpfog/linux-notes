## Ways to get the File System Type in Linux

###### If a file system already mounted or was mounted as a static file system

1. Run `mount` command without any arguments.
2. Run `df -Th` to include the file system type on a particular disk partition.
3. Run `cat /etc/fstab` to view static file system information.

###### If you know a name of a particular disk partition

1. Run `blkid /dev/sda3` to determine the partition properties.
2. Run `fsck -N /dev/sda3` and it just shows the file system type.
3. Run `file -sL /dev/sda3` to get details about the file system.

###### If you don't know what block devices you have in your system

1. Run `lsblk -f`
2. Run `ls -la /dev/disk/by-uuid` to get a list of UUID linked to partitions
