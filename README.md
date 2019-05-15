# Linux Server Configuration - Item Catalog App

### Introduction:

This project describes in detail on how to deploy the Flask App on Amazon Lightsail Ubuntu Instance. Below topics are covered:
1. Amazon Lightsail Instance Creation
2. New User Creation, Providing Sudo Access and Generating Public-Private RSA Key Pair
3. Change Port, Disable Root Access and Disable Password Authentication
4. Install Updates
5. Enabling the Default Ubuntu Firewall (UFW)
6. Install Apache Server, WSGI and GIT
8. Configure Apache to Serve Flask Application
9. Install and Setup Postgres Database
10. Update Google and Facebook OAuth Information

### Amazon Lightsail Instance Creation

1. Log into AWS Console [RegisteredUsers]('https://signin.aws.amazon.com/signin?redirect_uri=https%3A%2F%2Fconsole.aws.amazon.com%2Fconsole%2Fhome%3Fnc2%3Dh_ct%26src%3Dheader-signin%26state%3DhashArgs%2523%26isauthcode%3Dtrue&client_id=arn%3Aaws%3Aiam%3A%3A015428540659%3Auser%2Fhomepage&forceMobileApp=0'). If not please register to [NewRegistration]('https://portal.aws.amazon.com/billing/signup#/start')
1. Click on **Create Instance**
2. Select **OS Only**
3. Select **Ubuntu 16.04 LTS**
4. Select base instance 
5. Change the name if you would like but is not mandatory
6. Click on **Create Instance**
7. Go to **Account**
8. GO to SSH Keys and download the key into **/.ssh/** directory of your local machine.
9. Open Git Bash and type `ssh ubuntu@52.32.12.49 -i ~/.ssh/Lightsail.pem`

### New User Creation, Providing Sudo Access and Generating Public-Private RSA Key Pair 

**Note:** _Please make sure you are logged into the instance as ubuntu user_

1. `sudo adduser grader` - Creating User.
2. Enter password of your like, confirm and submit.
3. You can add values into fields: `Full Name`, `Room Number`, `Work Phone`, `Home Phone`, `Other` but are not mandatory.
4. `sudo nano /etc/sudoers.d/grader`- create a `sudoers.d` file for **grader**.
5. Enter `grader ALL=(ALL:ALL) ALL` and save the file.
6. Go back to your local machine and open another git bash console and execute command `ssh-keygen` to generate public-private RSA key pair for **grader** user.
7. Save the file into directory `/c/users/divvi/.ssh/item_catalog`. _Directory strucutre should be the same except the key file name. In my case it is `item_catalog`._
8. Give and confirm the passphrase.
9. `cat ~/.ssh/item_catalog.pub` - Copy the key generated.

**Note:** _Make sure your current working directory is /home/grader_

10. `mkdir .ssh`
11. `cd .ssh`
12. `sudo nano authorized_keys` - Create authorized_keys file.
13. Paste the key copied on **step 9** and save and exit the file.
14. `sudo chmod 700 .ssh` - Change permission of **.ssh** folder.
15. `sudo chmod 644 .ssh/authorized_keys` - Change permission of **authorized_keys** file.
16. `sudo chown -R grader:grader .ssh` - Change owner of **.ssh** folder to **grader**.

### Change Port, Disable Root Access and Disable Password Authentication

1. `sudo nano /etc/ssh/sshd_config` - Open the **sshd_config** file.
2. Change `Port 22` to `Port 2200`
3. Change `PermitRootLogin prohibit-password` to `PermitRootLogin no`
4. Change `PasswordAuthentication yes` to `PasswordAuthentication no`
5. `sudo service ssh restart` - Restart ssh service.
6. Go back to Amazon Lightsail and go to `Networking` tab.
7. Edit rules in **Firewall tab** by removing `port 22` and adding `port 2200(TCP)` and `port 123(UDP)`.

### Install Updates

1. `ssh grader@52.32.12.49 -p 2200 -i ~/.ssh/item_catalog` - Login to Linux Instance as a **grader** user.
2. Enter the passphrase you have created in **Step 8** of **New User Creation, Providing Sudo Access and Generating Public-Private RSA Key Pair**.
3. `sudo apt-get update` - Update the version number of all software's installed.
4. `sudo apt-get upgrade` - Install the updates.

### Enabling the Default Ubuntu Firewall (UFW)

1. `sudo ufw status` - Check the status of firewall, It should be **inactive**.
2. `sudo ufw default deny incoming`
3. `sudo ufw default allow outgoing`
4. `sudo ufw allow 2200/tcp`
5. `sudo ufw allow www`
6. `sudo ufw allow 123/udp`
7. `sudo ufw enable` and select **y**.

### Install Apache Server, WSGI and GIT

**Note:** _Please make sure you are logged into the instance as grader user_

1. `sudo apt-get install apache2` - Install apache2 server.
2. `sudo apt-get install libapach2-mod-wsgi python-dev` - Install WSGI.
3. `sudo a2enmod wsgi` - Enable mod.
4. `sudo apt-get install git` - Install GIT.

### Configure Apache to Serve Flask Application

1. `cd /var/www`
2. `sudo mkdir Catalog` - Create Catalog Directory.
3. `sudo chown -R grader:grader Catalog` - Change Owner.
4. `cd Catalog`
5. `sudo git clone https://github.com/SatishDivvi/Item-Catalog-Application.git Catalog` - Clone git repository and  name it **Catalog**.
6. `cd Catalog`
7. `mv application.py __init__.py` - Rename application.py file.

**Note:** _Below steps are to make git directory not accessible._

7. `cd .git`
8. `sudo nano .htaccess` - Create **.htaccess** file.
9. Enter text `RedirectMatch 404 /\.git` and save the file.

**Note:** _Below steps are to create virtual environment for our Item Catalog App_

10. `cd /var/www/Catalog/Catalog` - cd to Catalog project directory which was cloned.
11. `sudo apt-get install python-pip` - Install pip.
12. `sudo pip install virtualenv` - Install virtualenv.
13. `sudo virtualenv venv` - Create virtualenv.
14. `sudo chmod -R 777 venv` - Change virtualenv permission.
15. `source venv/bin/activate` - Activate venv.
16. `sudo pip install Flask` - Install Flask.
17. `sudo python __init__.py` - This step is merely needed to verify if all modules are successfully imported. If not it will provide you the module which is not imported.
18. `sudo pip install requests`
19. `sudo pip install sqlalchemy`
20. `sudo pip install oauth2client`
21. `sudo install psycopg2` - install postgres
22. `deactivate` -  deactivate the virtual environment i.e. **venv**.
23. `sudo nano /etc/apache2/sites-available/Catalog.conf` - Configure new Virtual Host.
24. Paste below code into the file:

```xml
<VirtualHost *:80>
                ServerName 54.185.239.30
                ServerAdmin admin@54.185.239.30
                WSGIScriptAlias / /var/www/Catalog/catalog.wsgi
                <Directory /var/www/Catalog/Catalog/>
                        Order allow,deny
                        Allow from all
                </Directory>
                Alias /static /var/www/Catalog/Catalog/static
                <Directory /var/www/Catalog/Catalog/static/>
                        Order allow,deny
                        Allow from all
                </Directory>
                ErrorLog ${APACHE_LOG_DIR}/error.log
                LogLevel warn
                CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

### Install and Setup Postgres Database

**Note:** _Please make sure you are logged into the instance as grader user_

1. `sudo apt-get install postgres` - Install postgres.
2. `sudo su - postgres` - login as a postgres user.
3. `psql` - Connect to shell.
4. `CREATE USER catalog WITH PASSWORD 'catalog';` - Create user catalog.
5. `ALTER USER catalog CREATEDB;` - Change catalog user role to creating database.
6. `CREATE DATABASE catalog with OWNER catalog;` - Create database catalog.
7. `\c catalog` - connect to catalog database.
8. `REVOKE ALL ON SCHEMA public FROM public;` - Revoke rights to all users.
9. `GRANT ALL ON SCHEMA public TO catalog;` - Grant rights to user **catalog** only.
10. `\q` - exit the datatabase.
11. `exit` - logout from postgres user account.
12. `cd /var/www/Catalog/Catalog/`
13. `sudo nano database_setup.py`
14. Change `create_engine('sqlite:///catalog.db')` to `create_engine('postgresql://catalog:catalog@localhost/catalog')` and save the file.
15. `sudo nano add_categories_and_items.py`
16. Change `create_engine('sqlite:///catalog.db')` to `create_engine('postgresql://catalog:catalog@localhost/catalog')` and save the file.
17. `sudo nano __init__.py`
18. Change `create_engine('sqlite:///catalog.db')` to `create_engine('postgresql://catalog:catalog@localhost/catalog')` and save the file.

### Update Google and Facebook OAuth Information

1. `cd /var/www/Catalog/Catalog`
2. `sudo nano __init__.py`
3. Change the path from `client_secrets.json` to `/var/www/Catalog/Catalog/client_secrets.json` wherever this file name is mentioned.
4. Change the path from `fb_client_secrets.json` to `/var/www/Catalog/Catalog/fb_client_secrets.json` wherever this file name is mentioned.
5. For Google OAuth:
    
    a) Add `http://52.32.12.49` and `http://ec2-52-32-12-49.us-west-2.compute.amazonaws.com` to **Javascript Origins**.
    b) Add `http://ec2-52-32-12-49.us-west-2.compute.amazonaws.com\login` and `http://ec2-52-32-12-49.us-west-2.compute.amazonaws.com/gconnect` or `http://ec2-52-32-12-49.us-west-2.compute.amazonaws.com/googleconnect` depnding on what route you have defined in `__init__.py`



Divvi Naga Venkata Satish - [Portfolio](https://satishdivvi.github.io)