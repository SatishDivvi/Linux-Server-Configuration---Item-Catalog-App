# Linux Server Configuration - Item Catalog App

### Introduction:

This project describes in detail on how to deploy the Flask App on Amazon Lightsail Ubuntu Instance. Below topics are covered:
1. Amazon Lightsail Instance Creation
2. Creation of new user and generating public-private RSA key pair
3. Enabling the default ubuntu firewall
4. Install app dependencies
5. Updating Google and Facebook OAuth if required
6. Installing Apache Server, changing it's configuration, WSGI
7. Cloning the Item Catalog App and changing database and client_secrets path
8. Installing Postgres and assigning to Item Catalog App

### Amazon Lightsail Instance Creation

1. Create Instance
2. Select OS Only
3. Select Ubuntu 16.04 LTS
4. Select base instance 
5. Change the name if you would like but not mandatory
6. Click on Create Instance
7. Go to account
8. GO to SSH Keys and download the key into /.ssh/ directory of your local machine.
9. Open Git Bash and type `ssh ubuntu@52.32.12.49 -i ~/.ssh/Lightsail.pem`


### OAuth Setup - Google


### Oauth Setup - Facebook



### Project Execution:



### Screenshots:



### Author

Divvi Naga Venkata Satish - [Portfolio](https://satishdivvi.github.io)