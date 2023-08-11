Install php 8.0, apache, mysql server and memcached on CentOS 8
```bash
# Enable Fastmirror
vi /etc/yum.conf
fastestmirror=1
max_parallel_downloads=8

yum install epel-release
yum update -y

yum module reset php
yum module enable php:8.0
```

```bash
# Install default packages
yum install php-opcache php-ffi php-pear.noarch php-pdo php-mysqlnd php-mbstring php-intl php-json php-fpm php-devel php-cli php-bcmath php-pecl-zip httpd memcached mysql-server

# Install php-memcached extension
yum install libmemcached-devel zlib-devel fastlz-devel libevent-devel libcurl-devel libxml2-devel
```
```bash
pecl install igbinary
vi /etc/php.d/40-igbinary.ini
    extension=igbinary.so

pecl install msgpack
vi /etc/php.d/40-msgpack.ini
    extension=msgpack.so

pecl install memcached
vi /etc/php.d/50-memcached.ini
    extension=memcached.so

# auto start services
systemctl enable php-fpm mysqld httpd memcached
systemctl start php-fpm mysqld httpd memcached
```
```bash
# set password
mysql -u root -p
    ALTER USER 'root'@'localhost' IDENTIFIED BY 'Mysql.123';
# Add apache user to vbox user
usermod -a -G vboxsf apache
```
```bash
vi /etc/my.cnf
#for mysql 8 with 1GB Ram

bind_address        = 0.0.0.0 # Change to 0.0.0.0 to allow remote connections
max_connect_errors  = 1000000

skip_external_locking
skip_name_resolve # Disabling DNS resolution
skip_ssl

interactive_timeout             = 10
wait_timeout                    = 10

innodb_buffer_pool_size         = 16M

log_queries_not_using_indexes   = 1     # Disabled on production
long_query_time                 = 5
slow_query_log                  = 1     # Disabled on production
slow_query_log_file             = /var/lib/mysql/mysql_slow.log
```
CentOS 9 Apache
```bash
vi /etc/httpd/conf/httpd.conf
EnableSendfile Off
```
On Ubuntu
```bash
sudo apt-get install virtualbox-guest-additions-iso
sudo apt install build-essential dkms
mount /usr/share/virtualbox/VBoxGuestAdditions.iso /mnt

sudo apt install php7.2-fpm php7.2-common php7.2-mysql php7.2-xml php7.2-xmlrpc php7.2-curl php7.2-gd php7.2-cli php7.2-dev php7.2-mbstring php7.2-soap php7.2-zip php7.2-bcmath php7.2-zip php7.2-tidy php7.2-opcache php7.2-intl php-pear php-solr php-memcached php-igbinary php-msgpack
apt install apache2
a2enmod actions fcgid alias proxy_fcgi headers rewrite

# Create a new Apache vhost configuration. 
sudo nano /etc/apache2/sites-available/domain.conf

# Now you can enable the new Apache configuration. 
sudo a2ensite domain.conf
usermod -a -G vboxsf www-data
```
