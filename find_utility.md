### Different ways to use `find` utility

1. Find all empty files in /dir/somedir
```bash
$ find /dir/somedir -size 0 -type f
```
2. Delete all zero byte files in /dir/somedir
```bash
$ find /dir/somedir -size 0 -type f -delete
```
3. Delete all files older than 1 week in /dir/somedir
```bash
$ find /dir/somedir -type f -mtime +7 -delete
```
4. Delete recursively directories older than 1 day in /dir/somedir
```bash
$ find /dir/somedir -type d -mtime +1 -prune -exec -r {} \;
```
5. Find all files in /dir/somedir created today
```bash
$ find /dir/somedir -type f -mtime -1
```
6. Find all symbolic links pointed to the directory /dir/somedir
```bash
$ find / -lname /dir/somedir
```
7. Change recursively the permissions of directories and files in the current directory
```bash
$ find . -type d -exec chmod 0775 {} \;
$ find . -type f -exec chmod 0664 {} \;
```
