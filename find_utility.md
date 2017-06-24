### Different ways to use `find` utility

1. Find all empty files in /dir/somedir
```console
$ find /dir/somedir -size 0 -type f
```
2. Delete all zero byte files in /dir/somedir
```console
$ find /dir/somedir -size 0 -type f -delete
```
3. Change recursively the permissions of directories and files in the current dir
```console
$ find . -type d -exec chmod 0775 {} \;
$ find . -type f -exec chmod 0664 {} \;
```
