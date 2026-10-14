# Zabbix 7.4 --- Ubuntu 26.04

## 1. Prerequisites

``` bash
sudo -s
apt update
apt install -y mysql-server apache2 php8.5-fpm php8.5-mysql wget
systemctl enable --now mysql apache2 php8.5-fpm
```

## 2. Zabbix Repository

``` bash
wget https://repo.zabbix.com/zabbix/7.4/release/ubuntu/pool/main/z/zabbix-release/zabbix-release_latest_7.4+ubuntu26.04_all.deb
dpkg -i zabbix-release_latest_7.4+ubuntu26.04_all.deb
apt update
```

## 3. Install Zabbix Server, Frontend and Agent

``` bash
apt install -y zabbix-server-mysql zabbix-frontend-php zabbix-apache-conf zabbix-sql-scripts zabbix-agent
```

## 4. Create Database

``` bash
mysql -uroot
```

Run:

``` sql
CREATE DATABASE zabbix CHARACTER SET utf8mb4 COLLATE utf8mb4_bin;
CREATE USER 'zabbix'@'localhost' IDENTIFIED BY 'YOUR_DB_PASSWORD';
GRANT ALL PRIVILEGES ON zabbix.* TO 'zabbix'@'localhost';
SET GLOBAL log_bin_trust_function_creators = 1;
EXIT;
```

**Replace `YOUR_DB_PASSWORD` with your own password.**

## 5. Import Database

``` bash
zcat /usr/share/zabbix/sql-scripts/mysql/server.sql.gz | mysql --default-character-set=utf8mb4 -uzabbix -p zabbix
```

Enter your own database password.

Then:

``` bash
mysql -uroot -e "SET GLOBAL log_bin_trust_function_creators = 0;"
```

## 6. Configure Database Password

``` bash
nano /etc/zabbix/zabbix_server.conf
```

Set:

``` text
DBPassword=YOUR_DB_PASSWORD
```

Replace `YOUR_DB_PASSWORD` with the same password used when creating the
database user.

## 7. Start Zabbix

``` bash
a2enmod proxy proxy_fcgi
systemctl enable --now zabbix-server zabbix-agent apache2 php8.5-fpm
```

## 8. Open Zabbix

Open in your browser:

``` text
http://YOUR_SERVER_IP/zabbix
```

Replace `YOUR_SERVER_IP` with your Ubuntu server/VM IP.

### Default Login

``` text
Username: Admin
Password: zabbix
```

Change the default password after the first login.

## Official Documentation

Zabbix 7.4:\
https://www.zabbix.com/documentation/7.4/en/

Zabbix Package Installation:\
https://www.zabbix.com/documentation/7.4/en/manual/installation/install_from_packages

Zabbix Database Scripts:\
https://www.zabbix.com/documentation/7.4/en/manual/appendix/install/db_scripts
