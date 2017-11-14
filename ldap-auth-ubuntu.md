
## Setting up LDAP auth on Ubuntu

### Define Locale and Language Settings

```console
# cat <<'EOF' > /etc/environment
LANG=en_US.utf-8
LC_ALL=en_US.utf-8
EOF
```

### Install utilites for access to your LDAP server (just for check an access)

```console
# apt-get update
# apt-get install ldap-utils
```

### Check an access to your LDAP server

```console
# ldapsearch -h 192.168.0.2 -x -b "uid=admin,cn=users,cn=compat,dc=contactis,dc=local" -D "cn=directory manager" -W
# ldapsearch -h 192.168.0.2 -x -b "uid=admin,cn=users,cn=compat,dc=contactis,dc=local" -D "uid=admin,cn=users,cn=compat,dc=contactis,dc=local" -W
```

The options meaning:

```text
-h - an IP address of the LDAP server
-b - a start point for the search ( an account of user admin for example ) 
-D - a Distinguished Name for binding to the LDAP directory ( a login to the LDAP server )
-W - prompt for simple authentication ( you will be asked to enter a password )

```

### Install LDAP authentication tools

```console
# apt-get install ldap-auth-client nscd
```

During the installation process you will be asked about some configuration parameters.  

Enter the URI of the LDAP server to use ( for example `ldap://192.168.0.2` )  
Enter the distinguished name of the LDAP search base ( for example `dc=contactis,dc=local` )  
Set LDAP version to use: `3`  
Make local root Database admin: `NO`  
Does the LDAP database require login: `YES`  
Unprivileged database user ( for example `uid=authsession,cn=users,cn=compat,dc=contactis,dc=local` )  
Enter a password for the user you set on the preview step.

### Set up /etc/nsswitch.conf to use ldap lookups

```console
# auth-client-config -t nss -p lac_ldap
```

It will use a configuration from `/etc/auth-client-config/profile.d/ldap-auth-config`.

### Getting group names from LDAP

To the file `/etc/ldap.conf` add a line:

```text
nss_base_group          cn=groups,cn=compat,dc=contactis,dc=local
```

### Automatically create home folders

Create a configuration profile

```console
# vim /usr/share/pam-configs/mkhomedir
```

with the text

```text
Name: Create home directory on login
Default: Yes
Priority: 0
Session-Type: Additional
Session-Interactive-Only: yes
Session:
	required			pam_mkhomedir.so umask=0022 skel=/etc/skel
```

### Assign local groups to LDAP users

Create a configuration profile

```console
# vim /usr/share/pam-configs/groups
```

with the text

```text
Name: Assign local groups to users on login
Default: Yes
Priority: 900
Auth-Type: Primary
Auth:
	required			pam_group.so use_first_pass
```

Local groups will be assigned by rules in `/etc/security/group.conf`.  
You can add the line

```text
*;*;*;Al0000-2400;users
```

for assigning the "users" group to all logged in users.

### Finalize and apply changes

```console
# pam-auth-update
```

Select your new profiles for creating home directories and assigning local groups on login.

```console
# /etc/init.d/nscd restart
# systemctl restart systemd-logind.service
```

