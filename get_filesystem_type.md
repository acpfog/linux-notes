## Ways to get the File System Type in Linux

### If a file system already mounted or was mounted as a static file system

- Run `mount` command without any arguments.
- Run `df -Th` to include the file system type on a particular disk partition.
- Run `cat /etc/fstab` to view static file system information.

### If you know a name of a particular disk partition

- Run `fsck -N /dev/sda3` and it just shows the file system type.
- Run `blkid /dev/sda3` to determine the partition properties.
- Run `file -sL /dev/sda3` to get details about the file system.

### If you don't know what block devices you have in your system

- Run `lsblk -f`
