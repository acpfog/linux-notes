
## How to set a locale and language configuration

If you got warning messages in CLI

```text
warning: setlocale: LC_CTYPE: cannot change locale (UTF-8): No such file or directory
```

Put the data into the file `/etc/environment`

```console
$ sudo cat <<'EOF' > /etc/environment
LANG=en_US.utf-8
LC_ALL=en_US.utf-8
EOF
```

and reconnect to the server.
