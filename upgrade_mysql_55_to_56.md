```console
wget https://dev.mysql.com/get/mysql-apt-config_0.8.9-1_all.deb
dpkg -i mysql-apt-config_0.8.9-1_all.deb ( select mysql 5.6 )
apt-get update
apt-get autoremove
systemctl stop puppet
systemctl stop nginx
systemctl stop php5-fpm
systemctl stop mysql
ps ax | egrep -e 'nginx|php|mysql'
cd /home/backup/
cp /etc/mysql/my.cnf /home/backup/
rsync -avh /var/lib/mysql /home/backup
ls -la mysql/
cat /home/backup/my.cnf 
dpkg -l | grep mysql
apt-get install mysql-community-server
apt-get upgrade libmysqlclient18
systemctl stop mysql
dpkg -l | grep mysql
dpkg --list |grep "^rc" | cut -d " " -f 3 | xargs sudo dpkg --purge
dpkg -l | grep mysql
vim /etc/mysql/mysql.conf.d/mysqld.cnf
systemctl start mysql
mysql_upgrade -u root -p
systemctl restart mysql
systemctl start php5-fpm
systemctl start nginx
systemctl restart collectd
systemctl start puppet
ps ax | egrep -e 'nginx|php|mysql'
apt-get autoremove
rm /etc/apparmor.d/local/usr.sbin.mysqld
rm /etc/rsyslog.d/default.conf
rm /var/log/mysql.*
```
