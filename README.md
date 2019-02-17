# ud-linuxserverconfiguration

# Project Description
You will take a baseline installation of a Linux server and prepare it to host your web applications. You will secure your server from a number of attack vectors, install and configure a database server, and deploy one of your existing web applications onto it.

# Project Overview
* IP Address: 52.91.99.71
* The complete URL to your hosted web application: ec2-52-91-99-71.us-east-1.compute.amazonaws.com

Locate the SSH key you created for the grader user.

During the submission process, paste the contents of the grader user's SSH key into the "Notes to Reviewer" field.

# Project Walkthrough
## Get your server.
* Start a new Ubuntu Linux server instance on Amazon Lightsail. There are full details on setting up your Lightsail instance on the next page.
* Follow the instructions provided to SSH into your server.

## Secure your server.
* Update all currently installed packages.
  ```
  sudo apt-get update
  sudo apt-get upgrade
  ```
* Change the SSH port from 22 to 2200. Make sure to configure the Lightsail firewall to allow it.
   Edit the ```etc/ssh/sshd_config``` file by using this command ```sudo nano /etc/ssh/sshd_config```.
   Change the port number on line 5 from 22 to 2200.
   Save and Exit using Ctl + X and then confirm with Y.
* Configure the Uncomplicated Firewall (UFW) to only allow incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123).
  ```
  sudo ufw allow 2200/tcp
  sudo ufw allow 80/tcp
  sudo ufw allow 123/udp
  sudo ufw enable
  ```
  ## Give Grader Access
* Create a new user account named grader.
  ```
  sudo adduser grader
  ```
* Give grader the permission to sudo.
  * Edit the subdoer file.
  ```
  sudo visudo
  ```
  * Add line to give grader the permission to sudo.
  ```
  grader  ALL=(ALL:ALL) ALL
  ```
  * Save and Exit using Ctl + X and then confirm with Y.
  
* Test sudo access.
  ```
  su -grader
  sudo -l
  ```
* Create an SSH key pair for grader using the ssh-keygen tool.
  * Run command ```ssh-keygen```.
  * Enter blank response for grader information.
  * Copy contents from .pub file.
  * Log into grader VM.

* Run commands.
  ```
  mkdir /home/grader/.ssh
  sudo nano ~/.ssh/authorized_keys
  chmod 700 /home/grader/.ssh
  chmod 644 /home/grader/.ssh/authorized_keys
  ```
 * Check ```/etc/ssh/sshd_config``` to verify if PasswordAuthentication is set to no.
 
 * Restart ```sudo service ssh restart```.
 
  ## Prepare to deploy your project.
* Configure the local timezone to UTC. 
  * Login to grader VM and run command ```sudo dpkg-reconfigure tzdata```. 
  Set to UTC.
* Install and configure Apache to serve a Python mod_wsgi application.
  * Run command ```sudo apt-get install apache2```.
* Install and configure PostgreSQL:
  * Run ```sudo apt-get install libapache2-mod-wsgi python-dev``` command.
  * Enable mod_wsgi with ```sudo a2enmod wsgi``` command.
  * Start the web server with ```sudo service apache2 start``` command.
* Install git.
  ```sudo apt-get install git```
 
 ## Deploy the Item Catalog project.
* Clone and setup your Item Catalog project from the Github repository you created earlier in this Nanodegree program.
  * Create ```/var/www/catalog/``` directory.
* Clone the catalog project:
 



 
