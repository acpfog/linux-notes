
## Setup an LDAP server with FreeIPA

FreeIPA provides a well managed Identity, Policy, and Audit (IPA) and consists from several services 389 Directory Server, MIT Kerberos, NTP, DNS, Dogtag (Certificate System), SSSD and others.
FreeIPA is supported by RedHat and works fine with Fedora.

### Install Fedora Server and make a minimal configuration

During an installation in "Base Environment" choose "Fedora Custom Operating System -> Standard".  
If your server uses a hostname with a local domain, add it to `/etc/hosts`.  
Set locale and language variables:

```console
# cat <<'EOF' > /etc/environment
LANG=en_US.utf-8
LC_ALL=en_US.utf-8
EOF
```

### Install FreeIPA server and related packages

```console
# dnf -y install freeipa-server
```

### Configure FreeIPA server

```console
# ipa-server-install --no-ntp --no_hbac_allow --idstart=33000 --idmax=45000 --hostname=freeipa.company.local -n company.local -r COMPANY.LOCAL -p passwd1 -a passwd2 -U
```

A description of the options:

```text
--no-ntp        - do not configure NTP
--no_hbac_allow - deny all defined users to access all defined services to and from all registered hosts
--idstart       - the starting user and group id number
--idmax         - the maximum user and group id number
--hostname      - the server's hostname
-n              - DNS domain name
-r              - the Kerberos realm name
-p              - a password to be used by the Directory Server for the Directory Manager user ("cn=directory manager")
-a              - a password for the IPA admin user (admin)
-U              - an unattended installation that will never prompt for user input
```

If you use a not real domain name, it might gives you an error during KDC restart, but the error should not affect a configuration process.

The configuration process will be finished when you see the lines:
```text
...
...
...
==============================================================================
Setup complete

Next steps:
        1. You must make sure these network ports are open:
                TCP Ports:
                  * 80, 443: HTTP/HTTPS
                  * 389, 636: LDAP/LDAPS
                  * 88, 464: kerberos
                UDP Ports:
                  * 88, 464: kerberos

        2. You can now obtain a kerberos ticket using the command: 'kinit admin'
           This ticket will allow you to use the IPA tools (e.g., ipa user-add)
           and the web user interface.

Be sure to back up the CA certificates stored in /root/cacert.p12
These files are required to create replicas. The password for these
files is the Directory Manager password
```

### Allow access to the FreeIPA services

```console
# firewall-cmd --permanent --add-service={http,https,ldap,ldaps,kerberos,kpasswd}
# firewall-cmd --reload
```

### Turn off anonymous binds

By default FreeIPA server allows anonymous requests to the Directory Server.  
To prevent a leak of critical information turn it off.

```console
# cat <<'EOF' > /tmp/anonoff.ldif
dn: cn=config
changetype: modify
replace: nsslapd-allow-anonymous-access
nsslapd-allow-anonymous-access: off
EOF
```

Change the LDAP server configuration.

```console
# ldapmodify -D "cn=directory manager" -w passwd1 -h freeipa.company.local -f /tmp/anonoff.ldif
```

Restart the Directory Server after configuration changes.

```console
# systemctl restart dirsrv.target
```

### Management through CLI

Get a Kerberos ticket ( with the password you set by -a option )
```console
# kinit admin
# klist
```

Get info about the admin user
```console
# ipa user-find admin
```

Change the default shell
```console
# ipa config-mod --defaultshell=/bin/console
```

### Management through Web UI

Open the link `https://freeipa.company.local/` in your browser. Log in with the user "admin" and the password you set by -a option. You can add a new account with login "username" for instance.

### Make requests to the LDAP server

Get information about the new user using the Directory Manager account

```console
# ldapsearch -h freeipa.company.local -x -b "uid=username,cn=users,cn=compat,dc=contactis,dc=local" -D "cn=directory manager" -w passwd1
```

Get information about the new user using the Admin account

```console
# ldapsearch -h freeipa.company.local -x -b "uid=username,cn=users,cn=compat,dc=contactis,dc=local" -D "uid=admin,cn=users,cn=compat,dc=contactis,dc=local" -w passwd2
```
