Linux Server Configuration

Project Description
Take a baseline installation of a Linux distribution on a virtual machine and prepare it to host the web applications, to include installing updates, securing it from a number of attack vectors and installing/configuring web and database servers.

> IP address: 18.194.41.215  
> Accessible SSH port: 2200  
> Application URL: http://18.194.41.215/main/  

1)Creating new user named grader and giving it the permission to sudo  
Run $ sudo adduser grader to create a new user named grader  
Create a new file in the sudoers directory with sudo nano /etc/sudoers.d/grader  
Add the following text grader ALL=(ALL:ALL) ALL  
Make folder using ```mkdir /home/grader/.ssh```  
Copy the public key to /home/grader/.ssh/authorized_keys with `sudo nano /home/grader/.ssh/authorized_keys`  
Disable ssh login for root user  
Run ```sudo nano /etc/ssh/sshd_config```  
Change ```PermitRootLogin without-password``` `line to PermitRootLogin no`  
Restart ssh with `sudo service ssh restart`  

2)Update all currently installed packages  
Download package lists with `sudo apt-get update`  
Fetch new versions of packages with `sudo apt-get upgrade`  

3)Change SSH port from 22 to 2200  
Run `sudo nano /etc/ssh/sshd_config`  
Change the port from 22 to 2200  

4)Configure the Uncomplicated Firewall (UFW) to only allow incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123)  
`sudo ufw allow 2200/tcp  
sudo ufw allow 80/tcp  
sudo ufw allow 123/udp  
sudo ufw enable  
`  
  
5)Configure the local timezone to UTC  
Run `sudo dpkg-reconfigure tzdata` and then choose UTC  

6)Install Apache  
`sudo apt-get install apache2`  

7)Install mod_wsgi  
Run `sudo apt-get install libapache2-mod-wsgi python-dev`  
Enable mod_wsgi with `sudo a2enmod wsgi`  
Start the web server with `sudo service apache2 start`  

8)Clone the Catalog app from Github  
Install git using: `sudo apt-get install git`  
`cd /var/www`  
`sudo mkdir catalog`  
Change owner of the newly created catalog folder `sudo chown -R grader:grader catalog  
cd /catalog`  
Clone the project from github `git clone https://github.com/Zomaldinho/Top-Clubs catalog`  
  
9)Create a catalog.wsgi file, then add this inside:  
```
import sys  
import logging  
logging.basicConfig(stream=sys.stderr)  
sys.path.insert(0, "/var/www/catalog/")  

from catalog import app as application  
application.secret_key = 'hoba'  
```

Rename application.py to init.py `sudo mv application.py __init__.py`  

10)Install virtual environment  
Install the virtual environment `sudo pip install virtualenv`  
Create a new virtual environment with `sudo virtualenv venv`  
Activate the virutal environment source `venv/bin/activate`  
Change permissions `sudo chmod -R 777 venv`  

11)Install Flask and other dependencies  
Install pip with `sudo apt-get install python-pip`  
Install Flask `sudo pip install Flask`  
Install other project dependencies `sudo pip install httplib2 oauth2client sqlalchemy psycopg2 sqlalchemy_utils`  
Update path of client_secrets.json file  
`nano __init__.py`  
Change client_secrets.json path to /var/www/catalog/catalog/client_secret.json  

12)Configure and enable a new virtual host  
Run this: `sudo nano /etc/apache2/sites-available/catalog.conf`  
Paste this code:  
```<VirtualHost *:80>  
    ServerName 18.194.41.215  
    ServerAdmin ubuntu@18.194.41.215  
    WSGIDaemonProcess catalog python-path=/var/www/catalog:/var/www/catalog/venv/lib/python2.7/site-packages  
    WSGIProcessGroup catalog  
    WSGIScriptAlias / /var/www/catalog/catalog.wsgi  
    <Directory /var/www/catalog/catalog/>  
        Order allow,deny  
        Allow from all  
    </Directory>  
    Alias /static /var/www/catalog/catalog/static  
    <Directory /var/www/catalog/catalog/static/>  
        Order allow,deny  
        Allow from all  
    </Directory>  
    ErrorLog ${APACHE_LOG_DIR}/error.log  
    LogLevel warn  
    CustomLog ${APACHE_LOG_DIR}/access.log combined  
</VirtualHost>  
```
Enable the virtual host sudo a2ensite catalog  
  
13)Install and configure PostgreSQL  
`sudo apt-get install libpq-dev python-dev`  
`sudo apt-get install postgresql postgresql-contrib`  
`sudo su - postgres`  
`psql`  
`CREATE USER catalog WITH PASSWORD 'hoba';`  
`ALTER USER catalog CREATEDB;`  
`CREATE DATABASE catalog WITH OWNER catalog;`  
`\c catalog`  
`REVOKE ALL ON SCHEMA public FROM public;`  
`GRANT ALL ON SCHEMA public TO catalog;`  
`\q`  
`exit`  
  
14)Change create engine line in your __init__.py and database_setup.py to:   
`engine = create_engine('postgresql://catalog:hoba@localhost/catalog')`  
`python /var/www/catalog/catalog/database_setup.py`  

15)Restart Apache  
`sudo service apache2 restart`  
