# Zabbix Installation Guide on Ubuntu 20.04.6 Focal

This guide walks you through the process of installing Zabbix on Ubuntu 20.04.6 Focal. The steps include setting up necessary dependencies, installing the Zabbix server, configuring the MySQL database, and accessing the Zabbix web interface.

## 1. Choose Your Platform

- Before you begin, decide which platform you want to install Zabbix on (e.g., Ubuntu, CentOS, Debian, etc.). In this instance, we are using Ubuntu 20.04.6 Focal. The installation steps may vary slightly based on your chosen platform.
- For more details, visit the official Zabbix download page: [Zabbix Downloads](https://www.zabbix.com/download)

## 2. Install Required Dependencies

Ensure that your system has the necessary dependencies installed. These typically include:

- Web Server (Apache or Nginx)
- Database Server (MySQL, PostgreSQL, or SQLite)
- PHP

Install these dependencies if they are not already present.

## 3. Download and Install Zabbix Repository

Add the official Zabbix repository to your system:

```bash
sudo wget https://repo.zabbix.com/zabbix/6.4/ubuntu/pool/main/z/zabbix-release/zabbix-release_6.4-1+ubuntu20.04_all.deb
sudo dpkg -i zabbix-release_6.4-1+ubuntu20.04_all.deb
sudo apt update
```

## 4. Install Zabbix Server, Frontend, and Agent Packages

```bash
sudo apt install zabbix-server-mysql zabbix-frontend-php zabbix-apache-conf zabbix-agent
```

Check the status of the server and agent:

```bash
sudo service zabbix-server status
sudo service zabbix-agent status
```

*Note: The server won’t be active yet; however, the agent status will show as active.*

## 5. Configure MySQL Database

Check if the MySQL database is running:

```bash
sudo service mysql status
```

Install MySQL Server:

```bash
sudo apt install mysql-server
sudo service mysql status
```

Log in to MySQL and set up the Zabbix database and user:

```bash
mysql

create database zabbix character set utf8mb4 collate utf8mb4_bin;
create user zabbix@localhost identified by 'password';
grant all privileges on zabbix.* to zabbix@localhost;

set global log_bin_trust_function_creators = 1;
quit;
```

Import the Zabbix schema:

```bash
zcat /usr/share/zabbix-sql-scripts/mysql/server.sql.gz | mysql --default-character-set=utf8mb4 -uzabbix -p zabbix
mysql

set global log_bin_trust_function_creators = 0;
quit;
```

## 6. Edit Configuration Files

Edit the Zabbix server configuration file and set the `DBPassword` property:

```bash
sudo nano /etc/zabbix/zabbix_server.conf

# Add the following line
DBPassword=password
```

## 7. Perform MySQL Checks

```bash
mysql

show databases;
use zabbix;
show tables;
select * from users;
quit;
```

## 8. Start Zabbix Server Process

Start the Zabbix Server process and check if it is active:

```bash
sudo service zabbix-server start
sudo service zabbix-server status
```

Ensure that Zabbix Server, Agent, and Apache restart in case of reboot:

```bash
systemctl enable zabbix-server zabbix-agent apache2
```

## 9. Configure Web Server

Restart the Apache2 service:

```bash
sudo service apache2 restart
```

Visit your new Zabbix Server at:

```
http://your-server-ip-address/zabbix
```

## 10. Access Zabbix Web Interface

Open a web browser and navigate to:

```
http://your_server_ip/zabbix
```

Follow the setup wizard:

- Set the database connection details
- Create an admin user and set the password
- Complete the wizard

## 11. Start Zabbix Agent

If you want to monitor the local server, start the Zabbix agent:

```bash
sudo systemctl start zabbix-agent
sudo systemctl enable zabbix-agent
```

## 12. Login to Zabbix

Log in to the Zabbix web interface using the admin credentials you set during the setup.

Congratulations! You have successfully installed and configured Zabbix on Ubuntu 20.04.6 Focal.