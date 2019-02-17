# ud-linuxserverconfiguration

# Project Description
You will take a baseline installation of a Linux server and prepare it to host your web applications. You will secure your server from a number of attack vectors, install and configure a database server, and deploy one of your existing web applications onto it.

# Project Overview
Create a new GitHub repository and add a file named README.md.

Your README.md file should include all of the following:
i. IP Address: 52.91.99.71
ii. The complete URL to your hosted web application: ec2-52-91-99-71.us-east-1.compute.amazonaws.com

Locate the SSH key you created for the grader user.

During the submission process, paste the contents of the grader user's SSH key into the "Notes to Reviewer" field.

# Get Started on Lightsail
1. Log in!
First, log in to Lightsail. If you don't already have an Amazon Web Services account, you'll be prompted to create one.

2. Create an instance.
Once you're logged in, Lightsail will give you a friendly message with a robot on it, prompting you to create an instance. A Lightsail instance is a Linux server running on a virtual machine inside an Amazon datacenter.

3. Choose an instance image: Ubuntu
Lightsail supports a lot of different instance types. An instance image is a particular software setup, including an operating system and optionally built-in applications.

For this project, you'll want a plain Ubuntu Linux image. There are two settings to make here. First, choose "OS Only" (rather than "Apps + OS"). Second, choose Ubuntu as the operating system.

4. Choose your instance plan.
The instance plan controls how powerful of a server you get. It also controls how much money they want to charge you. For this project, the lowest tier of instance is just fine. And as long as you complete the project within a month and shut your instance down, the price will be zero.

5. Give your instance a hostname.
Every instance needs a unique hostname. You can use any name you like, as long as it doesn't have spaces or unusual characters in it. Your instance's name will be visible to you and to the project reviewer.

6. Wait for it to start up.
It may take a few minutes for your instance to start up.

7. It's running; let's use it!
Once your instance has started up, you can log into it with SSH from your browser.

8. Project time.
Now that you have a working instance, you can get right into the project!

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
  * Edit the ```etc/ssh/sshd_config``` file by using this command ```sudo nano /etc/ssh/sshd_config```.
  * Change the port number on line 5 from 22 to 2200.
  * Save and Exit using Ctl + X and then confirm with Y.
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
  * Set to UTC.
 * Install and configure Apache to serve a Python mod_wsgi application.
  * Run command ```sudo apt-get install apache2```.
 * Install and configure PostgreSQL:
  * Run ```sudo apt-get install libapache2-mod-wsgi python-dev``` command.
  * Enable mod_wsgi with ```sudo a2enmod wsgi``` command.
  * Start the web server with ```sudo service apache2 start``` command.
 * Install git.
 
 ## Deploy the Item Catalog project.




 
