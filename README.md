# Linux Server Configuration - Item Catalog App

### Introduction:

This project describes in detail on how to deploy the Flask App on Amazon Lightsail Ubuntu Instance. Below topics are covered:
1. Amazon Lightsail Instance Creation
2. New User Creation, Providing Sudo Access and Generating Public-Private RSA Key Pair
3. Change Port, Disable Root Access and Disable Password Authentication
3. Enabling the default ubuntu firewall
4. Install app dependencies
5. Updating Google and Facebook OAuth if required
6. Installing Apache Server, changing it's configuration, WSGI
7. Cloning the Item Catalog App and changing database and client_secrets path
8. Installing Postgres and assigning to Item Catalog App

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

### Project Execution:



### Screenshots:



### Author

Divvi Naga Venkata Satish - [Portfolio](https://satishdivvi.github.io)