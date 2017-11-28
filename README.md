# Linux Server Configuration
Created a Linux server instance using Amazon Lightsail, secure and set up the Linux server. This is the sixth project of Udacity Full Stack Nanodegree program.

## IP address and SSH port
Public IP: 13.59.189.169
SSH port: 2200

## Complete URL to the web application
http://ec2-13-59-189-169.us-east-2.compute.amazonaws.com/

## List of software installed
+ libapache2-mod-wsgi-py3
+ PostgreSQL
+ git
+ mod_wsgi
+ python-pip
+ virtualenv
+ Flask
+ Flask-SQLAlchemy
+ requests
+ psycopg2
+ sqlalchemy
+ oauth2client

## Summary of configuration changes made
## Get server
Start a new Ubuntu Linux server instance on Amazon Lightsail based on Udacity instructions. 

## SSH into server
Download the defaut key from Amazon Lightsail account page. It is required that your private key files are NOT accessible by others.

`chmod 600 ~/Downloads/LightsailDefaultPrivateKey-us-east-2.pem`

To ssh into server:

`ssh -i ~/Downloads/LightsailDefaultPrivateKey-us-east-2.pem`

## Update all currently installed packages
`sudo apt-get update`

`sudo apt-get upgrade`

## change the SSH port from 22 to 2200
Open file and change 22 at the fifth line to 2200: 

`sudo nano /etc/ssh/sshd_config`

Restart SSH: 

`sudo service ssh restart`

## Set up firewall
`sudo ufw status`
Status: inactive
`sudo ufw default deny incoming`
Default incoming policy changed to 'deny'
(be sure to update your rules accordingly)
`sudo ufw default allow outgoing`
Default outgoing policy changed to 'allow'
(be sure to update your rules accordingly)
`sudo ufw allow ssh`
Rules updated
Rules updated (v6)
`sudo ufw allow 2200/tcp`
Rules updated
Rules updated (v6)
`sudo ufw allow www`
Rules updated
Rules updated (v6)
`sudo ufw allow 123/udp`
Rules updated
Rules updated (v6)
`sudo ufw deny 22`
Rules updated
Rules updated (v6)
`sudo ufw enable`
Command may disrupt existing ssh connections. Proceed with operation (y|n)? y
Firewall is active and enabled on system startup
`sudo ufw status`
Status: active

To                         Action      From
--                         ------      ----
22                         DENY        Anywhere                  
2200/tcp                   ALLOW       Anywhere                  
80/tcp                     ALLOW       Anywhere                  
123/udp                    ALLOW       Anywhere                  
22 (v6)                    DENY        Anywhere (v6)             
2200/tcp (v6)              ALLOW       Anywhere (v6)             
80/tcp (v6)                ALLOW       Anywhere (v6)             
123/udp (v6)               ALLOW       Anywhere (v6)             

## To ssh into server after port changing
`ssh ubuntu@13.59.189.169 -p 2200 -i ~/Downloads/LightsailDefaultPrivateKey-us-east-2.pem`

## Create a new user account named grader
ubuntu@ip-172-26-3-72:~$ `sudo adduser grader`
Adding user `grader' ...
Adding new group `grader' (1001) ...
Adding new user `grader' (1001) with group `grader' ...
Creating home directory `/home/grader' ...
Copying files from `/etc/skel' ...
Enter new UNIX password: 
Retype new UNIX password: 
passwd: password updated successfully
Changing the user information for grader
Enter the new value, or press ENTER for the default
    Full Name []: Udacity Linux Grader
    Room Number []: 
    Work Phone []: 
    Home Phone []: 
    Other []: 
Is the information correct? [Y/n] Y

When you are logged in as Ubuntu, and you want to temporarily “login” as grader:
`su - grader`
When you are done, 
`ctrl+d` or type in `exit` to return to login as Ubuntu


## Give grader the permission to sudo
`sudo visudo`

Add
`grader ALL=(ALL:ALL) ALL`
under this line 
`root ALL=(ALL:ALL) ALL`

`command + X` to exit, `Y` to save changes.

## Create an SSH key pair for grader using ssh-keygen tool
Generate key pair on local machine, not on the server.
Open a new terminal on local,run ssh-keygen to generate key pairs

$ `ssh-keygen`
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/greenroses/.ssh/id_rsa): 
/Users/greenroses/.ssh/linuxUdacityProject

On the local machine, read out the content of linuxUdacityProject.pub `cat .ssh/linuxUdacityProject.pub` and copy the content.

Install a public key:

ubuntu@ip-172-26-3-72:~$ `su - grader`
Password: 
grader@ip-172-26-3-72:~$ `mkdir .ssh`
grader@ip-172-26-3-72:~$ `touch .ssh/authorized_keys`
grader@ip-172-26-3-72:~$ `nano .ssh/authorized_keys`
paste in the content of the linuxUdacityProject.pub file
grader@ip-172-26-3-72:~$ `chmod 700 .ssh`
grader@ip-172-26-3-72:~$ `chmod 600 .ssh/authorized_keys` 
grader@ip-172-26-3-72:~$ `sudo nano /etc/ssh/sshd_config`
[sudo] password for grader: 
grader@ip-172-26-3-72:~$ `sudo service ssh restart`

To login in as grader: 
`ssh grader@13.59.189.169 -p 2200 -i ~/.ssh/linuxUdacityProject`

Forcing key based authentication:
`sudo nano /etc/ssh/sshd_config`
Look for `# Change to no to disable tunnelled clear text passwords`, and change its following line from `PasswordAuthentication yes` to `PasswordAuthentication no`.

Restart the service to apply the change:
`sudo service ssh restart`

##  Configure the local timezone to UTC.
grader@ip-172-26-3-72:~$ `sudo dpkg-reconfigure tzdata`

Current default time zone: 'US/Eastern'
Local time is now:      Thu Nov 16 03:28:12 EST 2017.
Universal Time is now:  Thu Nov 16 08:28:12 UTC 2017.

grader@ip-172-26-3-72:~$ `sudo dpkg-reconfigure tzdata`

Current default time zone: 'Etc/UTC'
Local time is now:      Thu Nov 16 08:29:18 UTC 2017.
Universal Time is now:  Thu Nov 16 08:29:18 UTC 2017.

## Install and configure PostgreSQL
`sudo apt-get install postgresql`

Create a new database user named catalog that has limited permissions to your catalog application database.
`sudo adduser catalog`

grader@ip-172-26-3-72:/var/www/catalog/catalog$ `sudo su - postgres`
postgres@ip-172-26-3-72:~$ `psql`
psql (9.5.10)
Type "help" for help.

postgres=# `ALTER USER catalog CREATEDB;`
ALTER ROLE
postgres=# `CREATE DATABASE categoryitemwithusers WITH OWNER catalog;`
CREATE DATABASE
postgres=# `\q`
postgres@ip-172-26-3-72:~$ `exit`
logout

## Install git
`sudo apt-get install git`
`git config --global user.name 'yourname'`
`git config --global user.email 'youremail'`

## Deploy flask app on ubuntu
1. install and enable mod_wsgi
`sudo apt-get install libapache2-mod-wsgi-py3`
`sudo a2enmod wsgi`

2. clone the Item Catalog project from the Github repository created earlier in this Nanodegree program
`cd /car/www`
`sudo mkdir catalog`
`cd catalog`
`sudo git clone https://github.com/greenroses/catalog.git`

change name of the application.py file to __init__.py
`mv application.py __init__.py`

After cloning the repository, the directory structure looks like:
----var
--------www
------------catalog
-----------------catalog
---------------------static
---------------------templates
---------------------__init__.py
---------------------...some other files


3. install flask
grader@ip-172-26-3-72:/$ `sudo apt-get install python-pip`
ubuntu@ip-172-26-3-72:/$ `sudo pip install virtualenv`
ubuntu@ip-172-26-3-72:/$ `sudo virtualenv venv`
ubuntu@ip-172-26-3-72:/var/www/catalog/catalog$ `sudo virtualenv venv`
`sudo chmod -R 777 venv`
ubuntu@ip-172-26-3-72:/var/www/catalog/catalog$ `source venv/bin/activate`
(venv) ubuntu@ip-172-26-3-72:/var/www/catalog/catalog$ `pip install Flask`

ubuntu@ip-172-26-3-72:/var/www/catalog/catalog$ `sudo apt-get install libapache2-mod-wsqi-py3`

ubuntu@ip-172-26-3-72:/var/www/catalog/catalog$ `sudo pip install Flask-SQLAlchemy`

4. configure a new virtual host
grader@ip-172-26-3-72:/$`sudo nano /etc/apache2/sites-available/catalog.conf`

add the following code to the file:
```
<VirtualHost *:80>
        ServerName 13.59.189.169
        ServerAdmin admin@13.59.189.169
        ServerAlias ec2-13-59-189-169.us-east-2.compute.amazonaws.com
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

In the file, the fourth line
`ServerAlias ec2-13-59-189-169.us-east-2.compute.amazonaws.com`
is added so that the app can be accessed by the url.

To enable the virtual host: 
grader@ip-172-26-3-72:/$ `sudo a2ensite catalog`

5. crete the .wsgi file

grader@ip-172-26-3-72:/$ `cd /var/www/catalog`
grader@ip-172-26-3-72:/var/www/catalog$ `sudo nano catalog.wsgi`

Add the following code to the file:
```
#!/usr/bin/python
import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0,"/var/www/catalog/")

from catalog import app as application
application.secret_key = 'Add your secret key'
```

now the directory looks like:
----var
--------www
------------catalog.wsgi
------------catalog
-----------------catalog
---------------------static
---------------------templates
---------------------venv
---------------------__init__.py
---------------------...some other files

6. restart apache to apply changes
`sudo service apache2 restart`


## Install some other softwares
Install `requests`:
ubuntu@ip-172-26-3-72:/var/www/catalog/catalog$ `sudo pip install requests`

Initially, psycopg2 was installed by:
(venv) ubuntu@ip-172-26-3-72:/var/www/catalog/catalog$ `pip install psycopg2`
(venv) ubuntu@ip-172-26-3-72:/var/www/catalog/catalog$ `pip3 install psycopg2`
ubuntu@ip-172-26-3-72:/var/www/catalog/catalog$ `pip install psycopg2`

`pip3` is used because I am using python3 and `pip` only installs for python2.

However, the following error occurred:
`ImportError: No module named 'psycopg2'`

After research and test, adding `-H` in the command fixed the error:
ubuntu@ip-172-26-3-72:/var/www/catalog/catalog$ `sudo -H pip3 install psycopg2`

Similarily, when trying to install sqlalchemy, flask and apache2 with `pip install` or `pip3 install`, I kept getting errors like `ImportError: No module named 'flask'` although flask was already installed. It seems that the program was not being able to access the installed flask. 

In the end, I installed `sqlalchemy, flask, apache2 and oauth2client` with

`sudo -H pip3 install`

And finally no error. 

Here, `pip3` is used because `pip` only installs for python2 and I am using python3 but have both python2 and python3 installed.

ubuntu@ip-172-26-3-72:/var/www/catalog/catalog$ `sudo -H pip3 install flask`

ubuntu@ip-172-26-3-72:/var/www/catalog/catalog$ `sudo -H pip3 install sqlalchemy`

ubuntu@ip-172-26-3-72:/var/www/catalog/catalog$ `sudo -H pip3 install psycopg2`

ubuntu@ip-172-26-3-72:/var/www/catalog/catalog$ `sudo -H pip3 install oauth2client`

## Deploy the Item Catalog project
## Set up catalog project in server so that it functions correctly when visiting the server’s IP address in a browser. 
1. Somehow in the process of installing software with `pip` and `pip3`, I decided to switch from python 2 to python 3. Here are the changes made: 
    In __init__.py file:
    | python 2  | python 3 |
    | --------- | -------- |
    | xrange    | range    |
    | print " " | print("")|
    |json.loads(h.request(url, 'GET')[1])|json.loads(h.request(url, 'GET')[1].decode('utf-8'))|

2. changes made in __init__.py file
    1)<del>`from database_setup import Base, Category, Item, User`</del>
    `from catalog.database_setup import Base, Category, Item, User`

    2) the last line: 
    <del>`app.run(host='0.0.0.0', port=8000)`</del>
    app.run()

    3) add full path for client_secrets.json file:
    `json.loads(
        open('/var/www/catalog/catalog/client_secrets.json', 'r')`

    4) change all 8000 in all the html files to the new url
    <del>`<a href="http://localhost:8000/"`</del>
    `<a href="http://ec2-13-59-189-169.us-east-2.compute.amazonaws.com"`

3. modify client_secrets.json for google sign in
    1) create a new project on Google API, and create a new client ID
    2) update client id in login.html file
    3) add the url and redirect url

        Authorized JavaScript origins:
        http://13.59.189.169
        http://ec2-13-59-189-169.us-east-2.compute.amazonaws.com

        Authorized redirect URIs:
        http://ec2-13-59-189-169.us-east-2.compute.amazonaws.com/login
        http://ec2-13-59-189-169.us-east-2.compute.amazonaws.com/logout
        http://ec2-13-59-189-169.us-east-2.compute.amazonaws.com/gconnect
        http://ec2-13-59-189-169.us-east-2.compute.amazonaws.com/gdisconnect

    4) download JSON, copy the content and paste into the client_secrets.json file

4. Run python lotsofitems.py to populate database
ubuntu@ip-172-26-3-72:/var/www/catalog/catalog$ `python lotsofitems.py`

## Make sure that your .git directory is not publicly accessible via a browser!
Put the following line in a .htaccess file at the root of your web server (/ver/catalog/):

`RedirectMatch 404 /\.git`

## Restart to apply changes
ubuntu@ip-172-26-3-72:/var/www/catalog/catalog$ `sudo service apache2 restart`

## Useful code
`sudo service apache2 restart`

`sudo tail /var/log/apache2/error.log`

`source venv/bin/activate`

`sudo -i -u postgres`

`psql`

The following decides whether the system is going to run the app with python2 or python3:

`sudo apt-get install libapache2-mod-wsqi-py3`

`sudo apt-get install libapache2-mod-wsgi python-dev`


## References
https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps

https://www.digitalocean.com/community/tutorials/how-to-install-and-use-postgresql-on-ubuntu-16-04

https://stackoverflow.com/questions/6142437/make-git-directory-web-inaccessible

http://sebastianraschka.com/Articles/2014_python_2_3_key_diff.html

https://discussions.udacity.com/t/importerror-no-module-named-flask-on-lightsail/397001/2

http://www.brianlinkletter.com/how-to-set-up-a-new-userid-on-your-amazon-aws-server-instance/

https://unix.stackexchange.com/questions/3568/how-to-switch-between-users-on-one-terminal