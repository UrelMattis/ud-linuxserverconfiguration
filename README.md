# ud-linuxserverconfiguration

# Project Description
You will take a baseline installation of a Linux server and prepare it to host your web applications. You will secure your server from a number of attack vectors, install and configure a database server, and deploy one of your existing web applications onto it.

# Project Overview
* IP Address: 52.91.99.71
* Accessible SSH port: 2200
* The complete URL to your hosted web application: ec2-52-91-99-71.us-east-1.compute.amazonaws.com

Locate the SSH key you created for the grader user.

During the submission process, paste the contents of the grader user's SSH key into the "Notes to Reviewer" field.

# Project Walkthrough
## Get your server.
1. Start a new Ubuntu Linux server instance on Amazon Lightsail. There are full details on setting up your Lightsail instance on the next page.
2. Follow the instructions provided to SSH into your server.

## Secure your server.
1. Update all currently installed packages.
  ```
  sudo apt-get update
  sudo apt-get upgrade
  ```
2. Change the SSH port from 22 to 2200. Make sure to configure the Lightsail firewall to allow it.
   Edit the ```etc/ssh/sshd_config``` file by using this command ```sudo nano /etc/ssh/sshd_config```.
   Change the port number on line 5 from 22 to 2200.
   Save and Exit using Ctl + X and then confirm with Y.
3. Configure the Uncomplicated Firewall (UFW) to only allow incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123).
  ```
  sudo ufw allow 2200/tcp
  sudo ufw allow 80/tcp
  sudo ufw allow 123/udp
  sudo ufw enable
  ```
  ## Give Grader Access
1. Create a new user account named grader.
  ```
  sudo adduser grader
  ```
2. Give grader the permission to sudo.
   Edit the subdoer file.
  ```
  sudo visudo
  ```
3. Add line to give grader the permission to sudo.
  ```
  grader  ALL=(ALL:ALL) ALL
  ```
   Save and Exit using Ctl + X and then confirm with Y.
  
4. Test sudo access.
  ```
  su -grader
  sudo -l
  ```
5. Create an SSH key pair for grader using the ssh-keygen tool.
  * Run command ```ssh-keygen```.
  * Enter blank response for grader information.
  * Copy contents from .pub file.
  * Log into grader VM.

6. Run commands.
  ```
  mkdir /home/grader/.ssh
  sudo nano ~/.ssh/authorized_keys
  chmod 700 /home/grader/.ssh
  chmod 644 /home/grader/.ssh/authorized_keys
  ```
 7. Check ```/etc/ssh/sshd_config``` to verify if PasswordAuthentication is set to no.
    * Change this line ```#PermitRootLogin yes``` to ```#PermitRootLogin no```.
 8. Restart ```sudo service ssh restart```.
 
  ## Prepare to deploy your project.
1. Configure the local timezone to UTC. 
  * Login to grader VM and run command ```sudo dpkg-reconfigure tzdata```. 
  Set to UTC.
2. Install and configure Apache to serve a Python mod_wsgi application.
  * Run command ```sudo apt-get install apache2```.
3. Install and configure PostgreSQL:
  * Run ```sudo apt-get install libapache2-mod-wsgi python-dev``` command.
  * Enable mod_wsgi with ```sudo a2enmod wsgi``` command.
  * Start the web server with ```sudo service apache2 start``` command.
4. Install git ```sudo apt-get install git```.
 
 ## Deploy the Item Catalog project.
1. Clone and setup your Item Catalog project from the Github repository you created earlier in this Nanodegree program.
  * Create ```/var/www/catalog/``` directory.
  * Clone the catalog project: ```sudo git clone https://github.com/UrelMattis/ud-itemcatalogproject.git```
2. Set up your server.
  * Create catalog.wsgi file and place text inside.
    ```
    import sys
    import logging
    logging.basicConfig(stream=sys.stderr)
    sys.path.insert(0, "/var/www/catalog/")

    from catalog import app as application
    application.secret_key = 'supersecretkey'
    ```
  * Rename application.py to init.py ```mv application.py __init__.py```.
  * Install and create virtual environment.
    ```
    sudo pip install virtualenv
    sudo virtualenv venv
    source venv/bin/activate
    sudo chmod -R 777 venv
    ```
  * Install Flask and other dependencies.
  * Install pip. 
  ```sudo apt-get install python-pip```.
  * Install flask ```pip install Flask```.
  * Install other dependencies. 
  ```
  sudo pip install httplib2 oauth2client sqlalchemy psycopg2 sqlalchemy_utils
  sudo pip install requests
  sudo pip install --upgrade oauth2client
  sudo apt-get install libpq-dev
  sudo pip install psycopg2
  ```
  * Configure and enable a new virtual host
  * Run command ```sudo nano /etc/apache2/sites-available/catalog.conf```
  * Insert code 
  ```
  <VirtualHost *:80>
    ServerName 52.91.99.71
    ServerAlias ec2-52-91-99-71.us-east-1.compute.amazonaws.com
    ServerAdmin admin@52.91.99.71
    WSGIDaemonProcess catalog python-path=/var/www/catalog:/var/www/catalog/venv/lib/python2.7/site-packages
    WSGIProcessGroup catalog
    WSGIScriptAlias / /var/www/catalog/catalog.wsgi
    <Directory /var/www/catalog/catalog/>
        Order allow,deny
        Allow from all
    </Directory>
    Alias /static /var/www/catalog/ud-itemcatalogproject/catalog/static
    <Directory /var/www/catalog/ud-itemcatalogproject/catalog/static/>
        Order allow,deny
        Allow from all
    </Directory>
    ErrorLog ${APACHE_LOG_DIR}/error.log
    LogLevel warn
    CustomLog ${APACHE_LOG_DIR}/access.log combined
  </VirtualHost>
  ```
  * Install and configure PostgreSQL
  ```
  sudo apt-get install libpq-dev python-dev
  sudo apt-get install postgresql postgresql-contrib
  sudo su - postgres
  psql
  CREATE USER catalog WITH PASSWORD 'catalog';
  ALTER USER catalog CREATEDB;
  CREATE DATABASE catalog WITH OWNER catalog;
  \c catalog
  REVOKE ALL ON SCHEMA public FROM public;
  GRANT ALL ON SCHEMA public TO catalog;
  ```
  * Restart Apache and launch app ```sudo service apache2 restart```.
  
  # Resources
  https://mediatemple.net/community/products/dv/204643810/how-do-i-disable-ssh-login-for-the-root-user
  https://www.youtube.com/watch?v=HcwK8IWc-a8
  http://httpd.apache.org/
 
