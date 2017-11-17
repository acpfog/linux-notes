## Setup MySQL replication ( master - slave ) for a database

### Configure the Master server

Make sure that there are lines in the configuration file `/etc/mysql/my.cnf`.

```text
#bind-address           = 127.0.0.1
server-id               = 1
log_bin                 = /var/log/mysql/mysql-bin.log
binlog_do_db            = database_name
```

If you comment `bind-address`, MySQL binds to all network interfaces.  
`server-id` is an unique number for the server in your replication group.  
`binlog_do_db` is what will be in the replication.  

If you remove `binlog_do_db` on the master server, all databases will be replicated. Then you can filter replicated databases on a slave server by this option.   

Restart MySQL server after changes in the configuration file.

```console
# service mysql restart
```

Grant access for the slave server with a user and a password.

```text
mysql> GRANT REPLICATION SLAVE ON *.* TO 'slave_user'@'%' IDENTIFIED BY 'password';
mysql> FLUSH PRIVILEGES;
```

### Prepare data for transfer from the Master server

Lock the database for prevent any changes.

```text
mysql> USE database_name;
mysql> FLUSH TABLES WITH READ LOCK;
```

Get the position from which the Slave will start replicating.

```text
mysql> SHOW MASTER STATUS;
+------------------+----------+----------------+------------------+
| File             | Position | Binlog_Do_DB   | Binlog_Ignore_DB |
+------------------+----------+----------------+------------------+
| mysql-bin.000001 |     2180 | database_name  |                  |
+------------------+----------+----------------+------------------+
1 row in set (0.00 sec)
```

Make a new shell connection to the Master server.  
If your database not very huge you can dump it into a file.

```console
# mysqldump -u root -p --opt database_name > /tmp/database_name.sql
```

Or make a copy of RAW files.

```console
# rsync -avh --progress /var/lib/mysql/database_name /tmp
```

In the MySQL client console make your database writeable again.

```text
mysql> UNLOCK TABLES;
```

### Transfer data to the Slave server

Copy the dump file

```console
# rsync -avh --progress -e "ssh -p 2222" /tmp/database_name.sql user@192.168.1.21:/tmp
```

or the database's RAW files to the Slave server.

```console
# rsync -avh --progress -e "ssh -p 2222" /tmp/database_name user@192.168.1.21:/tmp
```

You need an option -e "ssh -p 2222" if you use a non standard ssh port.

### Prepare the Slave server for the replication

All next steps do with the the Slave server.  
Stop MySQL for configuration changes and data manipulations.

```console
# service mysql stop
```

Make sure that there are lines in the configuration file `/etc/mysql/my.cnf`.

```text
server-id               = 2
relay-log               = /var/log/mysql/mysql-relay-bin.log
log_bin                 = /var/log/mysql/mysql-bin.log
binlog_do_db            = database_name
```

On the slave server the option `binlog_do_db` is a filter for a replication.  
You can put several `binlog_do_db` with several database names.  

If you made a copy of RAW files, set mysql owner and group for them.

```console
# chow -R mysql:mysql chown /tmp/database_name
```

Put the folder with them to `/var/lib/mysql`.

```console
# rsync -avh --progress /tmp/database_name /var/lib/mysql
```

Start MySQL after changes.

```console
# service mysql start
```

If you tranfer your database through a dump file, create the database and load the data.

```console
# mysql -u root -p -e 'CREATE DATABASE database_name'
# mysql -u root -p database_name < /tmp/database_name.sql
```

Check tables in your database if you tranfer your database through a copy of RAW files

```console
# mysqlcheck -u root -p database_name
```

If you have some problems with the tables, repair them using mysqlcheck.

### Turn on the replication

Enable the replication.

```text
mysql> CHANGE MASTER TO MASTER_HOST='192.168.1.20', MASTER_USER='slave_user', MASTER_PASSWORD='password', MASTER_LOG_FILE='mysql-bin.000001', MASTER_LOG_POS=2180;
mysql> START SLAVE;
```

Use the appropriate values for MASTER_HOST, MASTER_USER and MASTER_PASSWORD.
Values for MASTER_LOG_FILE and MASTER_LOG_POS take from the output of 'SHOW MASTER STATUS' before data dumping.

Check the replecation status.

```text
mysql> SHOW SLAVE STATUS\G
```

The `\G` rearranges the text to make it more readable.  

Don't forget to remove all data copies from the `/tmp` folder on all servers.

