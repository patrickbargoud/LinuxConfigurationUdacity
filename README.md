Linux Configuration - Patrick Bargoud

ABOUT
- IP Address: 35.183.42.71
- SSH Port: 2200
- URL: http://35.183.42.71.xip.io
- Software Installed: Apache, Git, Pip, Flask, PostgreSQL, SQLAlchemy, Google OAuth, Psycopg2
- Grader password is "grader"

CONFIGURATION STEPS
Amazon Lightsail
1. Download the .pem key from Amazon Lightsail
2. Move key into ~/.ssh folder
3. SSH into the server using the command ssh -i ~/.ssh/[pem-key] ubuntu@35.183.42.71
4. sudo apt-get update
5. sudo apt-get upgrade
6. sudo apt-get -s dist-upgrade

AMAZON LIGHTSAIL
1. sudo vi /etc/ssh/sshd_config
2. Change Port 22 to Port 2200
3. sudo service ssh restart
4. SSH into the server using ssh -i ~/.ssh/[pem-key] ubuntu@54.159.15.167 -p 2200
5. sudo ufw allow 2200/tcp
6. sudo ufw allow 80/tcp
7. sudo ufw allow 123/udp
8. sudo ufw enable
9. Go to networking in lightsail and add the following connections:
- Port 80
- Port 123
- Port 2200

ADD GRADER USER
1. sudo adduser grader
2. vi /etc/sudoers.d/grader
3. Type grader ALL=(ALL:ALL) ALL . Save and quit
4. sudo su grader
5. mkdir .ssh
6. touch .ssh/authorized_keys
7. chmod 700 .ssh
8. chmod 600 .ssh/authorized_keys
9. cp /home/ubuntu/.ssh/authorized_keys /home/grader/.ssh/authorized_keys
10. sudo service ssh restart
11. SSH into the VM as grader using ssh -i ~/.ssh/[pem-key] grader@35.183.42.71
12. sudo timedatectl set-timezone UTC

POSTGRESQL CONGIFURATION
1. sudo apt-get install postgresql
2. sudo su postgres
3. psql
4. In the shell, run the following commands:
- CREATE USER catalog WITH PASSWORD 'password';
- ALTER USER catalog CREATEDB;
- CREATE DATABASE catalog WITH OWNER catalog;
- \c catalog
- REVOKE ALL ON SCHEMA public FROM public;
- GRANT ALL ON SCHEMA public TO catalog;
- \q
- exit
5. Add create_engine('postgresql://catalog:password@localhost/catalog') to your files.

FLASK APP
1. sudo apt-get install apache2
2. sudo apt-get install libapache2-mod-wsgi python-dev
3. sudo a2enmod wsgi
4. sudo service apache2 start
5. sudo apt-get install git
6. cd /var/www
7. sudo mkdir catalog
8. cd catalog
9. git clone [repository-directory]
10. mv [repository-directory]/catalog/
11. Change main application to __init__.py
12. sudo apt-get install python-pip
13. sudo pip install virtualenv
14. source venv/bin/activate
15. sudo pip install Flask
16. sudo pip install httplib2
17. sudo pip install requests
18. sudo pip installoauth2client
19. sudo pip install sqlalchemy
20. sudo pip install psycopg2
21. sudo pip install sqlalchemy_utils

VIRTUAL HOST CONFIGURATION
1. sudo vi /etc/apache2/sites-available/catalog.conf
2. Add the following:
<VirtualHost *:80>
		ServerName [IP]
		ServerAdmin admin@[IP]
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

3. sudo a2ensite catalog

CREATE .WSGI FILE
1. cd /var/www/catalog
2. sudo vi catalog.wsgi
3. Add the following:
#!/usr/bin/python
import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0,"/var/www/catalog/")

from catalog import app as application
application.secret_key = 'super_secret_key'

4. sudo service apache2 restart


ISSUES I RAN INTO
- Could not SSH into the VM
	- Make sure Networking Tab is configured properly
- Module not found
	- Make sure you pip install all dependencies
	- Sometimes uninstalling and installing fixes it
- Google Login not working
	- Make sure IP Address is added in files and xip.io is configured











