## Linux Configuration Project

### Introduction

In this project, I configured a linux Ubuntu server on AWS Lightsail VM and hosted web and db on the server to run an Item Catalog web application.

### Instruction 
To ssh in into the server as the user **grader**, use the private key provided and the following server info:

  Server IP: 13.250.121.47
  SSH port: 2200
  
To access the web application with a web browser, use the URL: http://http://13.250.121.47.xip.io

### Server Configuration

**Update system packages: **
  `sudo apt-get update`
  `sudo apt-get upgrade`
  `sudo apt-get dist-upgrade` - to upgrade OS packages
    
**Configure timezone to UTC:**
  `sudo dpkg-reconfigure tzdata`
  and choose UTC from the list
    
**Change ssh port from 22 to 2200 and disable root login from ssh:**
  `sudo nano /etc/ssh/sshd_config`
  Change the line `Port 22` to `Port 2200`
  and `PermitRootLogin yes` to `PermitRootLogin no`
  and restart ssh service with `service sshd restart`

**Configure firewall to enable only Port 2200 for ssh, http and ntp**

  Disable all incoming traffic `sudo ufw default deny incoming`
  Enable all outgoing traffic `sudo ufw default allow outgoing`
  Enable port 2200: `sudo ufw allow 2200/tcp`
  Enable http on port 80: `sudo ufw allow www`
  Enable ntp: `sudo ufw allow ntp`
  Enable firewall rules: `sudo ufw enable`

**Add a new user for Udacity grader and add the user sudo right:**
  `sudo adduser grader`
  `sudo usermod -aG sudo grader`
  
**Generate a key pair and use it for grader login:**
  
  `keygen -t rsa /localsshfolder`
  Create a folder .ssh on grader home dir and paste the pub key into the folder:
  ```
  mkdir .ssh
  touch .ssh/authorized_keys
  nano .ssh/authorized_keys
  chmod 700 .ssh`
  chmod 644 .ssh/authorized_keys
  ```
   
**Install apache, postgres, wgsi and relevant services and modules for web application**
  ```
  sudo apt-get install postgresql
  sudo apt-get install apache2
  sudo apt-get install libapache2-mod-wsgi
  sudo apt-get install python-dev python-pip
  sudo pip install Flask
  sudo pip install --upgrade google-api-python-client
  sudo pip install --upgrade oauth2client
  sudo pip install --upgrade sqlalchemy
  sudo pip install requests
  ```
  
**Configure wgsi to let apache server run wgsi app on /var/www/html/catalog/**
  ```
  sudo nano /etc/apache2/sites-enabled/000-default.conf 
  sudo apache2ctl restart
  ```
  
**DB Configuration: Create a postgresql user catalog with limited rights, and change authentication mode to password**
  ```
  sudo -u postgres psql postgres
  postgres=# CREATE ROLE catalog CREATEDB LOGIN CONNECTION LIMIT -1 PASSWORD 'password';
  sudo -u postgres psql postgres
  \password postgres
  sudo nano /etc/postgresql/9.5/main/pg_hba.conf
  sudo service postgresql restart 
  ```
  
**Create Database for the web app**
  ```
  psql postgres -U catalog
  CREATE DATABASE catalogitemswithusers;
  ```
  
**Create repo and sync code for catalog item app, configure __init.py**
  ```
  sudo git init
  sudo git clone https://github.com/liwen-hou/catalog-item-app
  sudo cp project.py __init__.py
  ```
  and change path for client_secret.json in __init__.py to /var/www/html/catalog/client_secret.json
  
**Create a dir for python-eggs**
  ```
  mkdir /var/www/.python-eggs
  sudo mkdir /var/www/.python-eggs
  sudo chmod 777 /var/www/.python-eggs
  ```

