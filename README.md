Linux Server Configuration

Project Description
Take a baseline installation of a Linux distribution on a virtual machine and prepare it to host the web applications, to include installing updates, securing it from a number of attack vectors and installing/configuring web and database servers.

> IP address: 18.194.41.215  
> Accessible SSH port: 2200  
> Application URL: http://18.194.41.215/main/  

1)Creating new user named grader and giving it the permission to sudo  
Run `sudo adduser grader to create a new user named grader`  
Create a new file in the sudoers directory with `sudo nano /etc/sudoers.d/grader`  
Add the following text `grader ALL=(ALL:ALL) ALL`  
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
Install Flask `sudo pip install 
