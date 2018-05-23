## sshguard

### Add an IP address to the whitelist

If your IP address by some reasons is blocked, add it to the whitelist.
The whitelist is in the file `/etc/sshguard/whitelist`.
And restart sshguard

```console
# systemctl restart sshguard
```

### List blocked IP addresses

How to list blocked IP addresses by sshguard

```console
# iptables -nvL sshguard
```

