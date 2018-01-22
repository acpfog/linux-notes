## Manage packages in Debian/Ubuntu

### Hold a package or remove the hold

Holding a package basically means you're telling the package manager to keep the current version no matter what. This is useful if more recent version of a currently working program breaks after an update.

```console
$ sudo apt-mark hold <package-name>
$ sudo apt-mark unhold <package-name>
```
