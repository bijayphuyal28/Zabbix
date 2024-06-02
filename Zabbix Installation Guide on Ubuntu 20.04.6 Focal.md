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

Add the official Zabbix repository to your system and update the package and dependancy information:

```bash
sudo wget https://repo.zabbix.com/zabbix/6.4/ubuntu/pool/main/z/zabbix-release/zabbix-release_6.4-1+ubuntu20.04_all.deb
```

```bash
sudo dpkg -i zabbix-release_6.4-1+ubuntu20.04_all.deb
```

```bash
sudo apt update
```

## 4. Install Zabbix Server, Frontend, and Agent Packages

```bash
sudo apt install zabbix-server-mysql zabbix-frontend-php zabbix-apache-conf zabbix-agent
```

Check the status of the server and agent:

```bash
sudo service zabbix-server status
```

```bash
sudo service zabbix-agent status
```

*Note: The server won’t be active yet; however, the agent status will show as active.*

## 5. Configure MySQL Server and Database

Check if the MySQL is running:

```bash
sudo service mysql status
```

Install MySQL Server and check status whether mysql server and agent are running:

```bash
sudo apt install mysql-server
```

```bash
sudo service mysql status
```

Log in to MySQL and set up the Zabbix database and user:

```bash
mysql
```

```bash
create database zabbix character set utf8mb4 collate utf8mb4_bin;
```

```bash
create user zabbix@localhost identified by 'password';
```

```bash
grant all privileges on zabbix.* to zabbix@localhost;
```


## 6. Import the Zabbix schema:

Since August 2022, we need to temporarily enable the log_bin_trust_function_creators option to import the zabbix schema

```bash
set global log_bin_trust_function_creators = 1;
```

```bash
quit;
```

we can now proceed with importing the zabbix schema and disable the log_bin_trust_function_creators option

```bash
zcat /usr/share/zabbix-sql-scripts/mysql/server.sql.gz | mysql --default-character-set=utf8mb4 -uzabbix -p zabbix
```

```bash
mysql
```

```bash
set global log_bin_trust_function_creators = 0;
```

```bash
quit;
```

## 6. Edit Configuration Files and add the following line

Edit the Zabbix server configuration file and set the `DBPassword` property:

```bash
sudo nano /etc/zabbix/zabbix_server.conf
```

DBPassword=password


## 7. Perform MySQL Checks

```bash
mysql
```

```bash
show databases;
```

```bash
use zabbix;
```

```bash
show tables;
```

```bash
select * from users;
```

```bash
quit;
```

## 8. Start Zabbix Server Process

Start the Zabbix Server process and check if it is active:

```bash
sudo service zabbix-server start
```

```bash
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
```

```bash
sudo systemctl enable zabbix-agent
```

## 12. Login to Zabbix

Log in to the Zabbix web interface using the admin credentials you set during the setup.

Congratulations! You have successfully installed and configured Zabbix on Ubuntu 20.04.6 Focal.
