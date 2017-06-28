## Different ways to use `find` utility

###### Find all empty files in /dir/somedir
```console
host:~$ find /dir/somedir -size 0 -type f
```
###### Delete all zero byte files in /dir/somedir
```console
host:~$ find /dir/somedir -size 0 -type f -delete
```
###### Delete all files older than 1 week in /dir/somedir
```console
host:~$ find /dir/somedir -type f -mtime +7 -delete
```
###### Delete recursively directories older than 1 day in /dir/somedir
```console
host:~$ find /dir/somedir -type d -mtime +1 -prune -exec -r {} \;
```
###### Find all files in /dir/somedir created today
```console
host:~$ find /dir/somedir -type f -mtime -1
```
###### Find all symbolic links pointed to the directory /dir/somedir
```console
host:~$ find / -lname /dir/somedir
```
###### Change recursively the permissions of directories and files in the current directory
```console
host:~$ find . -type d -exec chmod 0775 {} \;
host:~$ find . -type f -exec chmod 0664 {} \;
```
