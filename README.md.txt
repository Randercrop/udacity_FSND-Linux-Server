# Udacity FSND Linux Server Configuration

View the server running here: http://34.220.230.136.nip.io

# Install and Update packages
- `sudo apt-get update`
- `sudo apt-get upgrade`


# User Management
1. `sudo adduser grader`, create grader user with a password 'password'
2. `sudo visudo` and add a line for `grader ALL=(ALL:ALL) ALL`
3. `sudo nano /etc/ssh/sshd_config` and set PermitRootLogin to no

## To enable grader to ssh
1. create public and private keys using ssh-keygen, and save it to a local directory
1. `su - grader`
2. `sudo nano .ssh/authorized_keys`
3. copy the public key into this file
4. Now you can ssh into the grader acount from terminal with `ssh -i (path/to/key/grader_ssh) grader@34.220.230.136 -p 2200`

# Application Functionality
1. `sudo mkdir /var/www/FlaskApp`
2. `cd /var/www/FlaskApp`
3. `sudo git clone https://github.com/Randercrop/udacity_FSND-catalog.git`
4. `mv udacity_FSND-catalog FlaskApp`
5. `cd FlaskApp`
6. `mv application.py __init__.py`
7. `sudo virtualenv venv`
8. `source venv/bin/activate` to launch the virual environment
9. On the new virtual environment, I installed Flask and other packages needed to run my catalog application
10. enable virtual host with `sudo a2ensite FlaskApp`

# Configure WSGI
1. `sudo nano /etc/apache2/sites-available/FlaskApp.conf`
`<VirtualHost *:80>
		ServerName 34.220.230.136
		ServerAdmin *****
		WSGIScriptAlias / /var/www/FlaskApp/flaskapp.wsgi
		<Directory /var/www/FlaskApp/FlaskApp/>
			Order allow,deny
			Allow from all
			WSGIApplicationGroup %{GLOBAL}
		</Directory>
		Alias /static /var/www/FlaskApp/FlaskApp/static
		<Directory /var/www/FlaskApp/FlaskApp/static/>
			Order allow,deny
			Allow from all
		</Directory>
		ErrorLog ${APACHE_LOG_DIR}/error.log
		LogLevel warn
		CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>`

2. `sudo nano flaskapp.wsgi`
`#!/usr/bin/python
import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0,"/var/www/FlaskApp/")
sys.path.insert(0,"/var/www/FlaskApp/FlaskApp/")

from FlaskApp import app as application
application.secret_key = 'super_secret_key'`

3. restart apache with `sudo service apache2 restart`

# Modify application to utilize google oauth
1. Append magic domain `.nip.io` to the end of my public url to make it accepted by google auth
2. Add `ServerAlias (found 34.220.230.136.nip.io)` to my apache conf file /etc/apache2/sites-available/FlaskApp.conf
3. Add the new hostname to the google developer oauth console

# List of installed software
- finger : `sudo apt-get install finger`
- apache : `sudo apt-get install apache2`
- Mod_wsgi : `sudo apt-get install libapache2-mod-wsgi-py3`
- pip : `sudo apt-get install python-pip`
- virtualenv : `sudo pip install virtualenv`
- Flask : `sudo pip install Flask`
- sqlAlchemy : `sudo pip install sqlAlchemy` This was needed for the application
- oauth2client : `sudo pip install oauth2client` This was needed for the application
- requests : `sudo pip install requests` This was needed for the application

## Resources used
- [Deploy Flask App to Digital Ocean with Python3](http://leonwang.me/post/deploy-flask)
- [How To Deploy a Flask Application on an Ubuntu VPS](https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps)