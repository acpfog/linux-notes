## How to measure a hard disk performance

###### Using `hdparm`
```console
host:~# hdparm -Tt /dev/md0
host:~# hdparm -Tt --direct /dev/md0
```
###### Using `dd`
```console
host:~# cd /tmp

host:~# dd if=/dev/zero of=testfile0 bs=8k count=10000; sync;
host:~# dd if=/dev/urandom of=testfileR bs=8k count=10000; sync;

host:~# dd if=testfile0 of=/dev/null
host:~# dd if=testfileR of=/dev/null

host:~# rm testfileR
host:~# rm testfile0
```
