# 1. Install Zabbix Server and Zabbix Agent Documentation on CentOS 7:
``` 
rpm -Uvh https://repo.zabbix.com/zabbix/5.0/rhel/7/x86_64/zabbix-release-5.0-1.el7.noarch.rpm

```

# Lets Clean Yum Yummy U+1F600
```
yum clean all

```

-------------------------------------------------------------

# 2. Install Zabbix server, frontend, agent

```
yum install zabbix-server-mysql zabbix-agent

```

-------------------------------------------------------------

# 3. Install Zabbix frontend

# Enable Red Hat Software Collections

```
yum install centos-release-scl
```

# Edit file /etc/yum.repos.d/zabbix.repo and enable zabbix-frontend repository.

[zabbix-frontend]

...

enabled=1

...


```
vim /etc/yum.repos.d/zabbix.repo

```



# Install Zabbix frontend packages.

```
yum install zabbix-web-mysql-scl zabbix-apache-conf-scl
```


-------------------------------------------------------------

# 4. Create initial database

Installing MariaDB:

First Update

```
sudo yum update
```
Install MariaDB Server

```
sudo yum install mariadb-server
```

You can login into mariadb server by following command. 

```
mysql
```

Or do following for set root password

```
mysql
```
Switch to mysql database.

```
use mysql;
```

```
SET PASSWORD FOR 'root'@localhost = PASSWORD("your_root_password");
```

```
FLUSH PRIVILEGES;
```

Now get out from mariaDB Server quickly by following command.

```
quit;
```

-------------------------------------------------------------

# 5. Now add zabbix database and zabbix user And Make sure you have database server up and running.

Run the following on your database host.

```
mysql -u root -p
```

your_root_password

mysql> create database zabbix character set utf8 collate utf8_bin;

mysql> create user zabbix@localhost identified by 'your_root_password';

mysql> grant all privileges on zabbix.* to zabbix@localhost;

mysql> set global log_bin_trust_function_creators = 1;

mysql> quit;


On Zabbix server host import initial schema and data. You will be prompted to enter your newly created password.

```
zcat /usr/share/doc/zabbix-server-mysql*/create.sql.gz | mysql -uzabbix -p zabbix
```

Disable log_bin_trust_function_creators option after importing database schema.

```
mysql -u root -p
```

your_root_password

```
set global log_bin_trust_function_creators = 0;
```

```
quit;
```
-------------------------------------------------------------

# 6. Configure the database for Zabbix server

Edit file /etc/zabbix/zabbix_server.conf

```
vim /etc/zabbix/zabbix_server.conf
```

DBPassword=password

-------------------------------------------------------------


# 7. Configure PHP for Zabbix frontend

Edit file /etc/opt/rh/rh-php72/php-fpm.d/zabbix.conf uncomment and set the right timezone for you.

```
vim /etc/opt/rh/rh-php72/php-fpm.d/zabbix.conf
```

; php_value[date.timezone] = Asia/Kolkata


-------------------------------------------------------------


# 8. Start Zabbix server and agent processes

Start Zabbix server and agent processes and make it start at system boot.

```
systemctl restart zabbix-server zabbix-agent httpd rh-php72-php-fpm
```

```
systemctl enable zabbix-server zabbix-agent httpd rh-php72-php-fpm
```