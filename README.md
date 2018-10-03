# AWSLabs
Learning and documenting how to use AWS

There is two ways to complete most if not all the actions here. The first time I go through my learning, I will be doing it through the console. I eventually plan to go back through all the steps and to automate everything. I haven't decided if I want to do this after each step or at the end of everything.

## Account Basics

#### IAM Users

Creating IAM users are important as it allows multiple users using your AWS account without giving your own account details. Additionally, creating an IAM user for yourself is best practice as this adds a bit more security so that if the root account gets comprimised, much won't be compromised.

1) Sign into the AWS Console and go to the IAM Console.
2) Choose Users -> Add User.
3) File in a name for the account. Multiple users can be created at this stage.
4) Select AWS Management Console as the access type. Also, select Autogenerated password for the password type.
5) At this point, a user can be added to a group, copy permissions from another user or attach an existing policy to the user. I decided to add the user to an Administrator group.
6) Review changes at this stage and verify if everything is correct. If it is an email can be sent to the user, via email, with their log in credentials.

The new user is now setup and ready to be used.

#### Root User Management

The next step is securing the root user for AWS. The method I will be using is a virtual type of authentication with Google Authenticator. While hardware type authentication is more secure, I did not have access to any FOB. If access to an authenticator is lost, the AWS support team can be contacted for further steps.

1) Sign into the AWS Console, choose Dashboard -> Security Status -> Activate MFA on your root user.
2) Select Activate MFA and then continue.
3) Select Virtual MFA Device and then continue. At this point a virtual MFA app should already be installed.
4) The easiest way to setup MFA is to scan the QR Code on screen with your phone. 2 separate MFA codes will need to be entered.
5) Select Assign MFA and then Finish.

MFA is now setup on the root user for AWS. The same thing can be done with a regular IAM user.

##### Note: For the next sub-section, I won't be providing steps as it's more simple to just look it up in the AWS user guide. I will add this note whenever I'm going to skip the steps portion.

#### Billing Alerts

Setting up billing alerts is really important as you do not want to go over your budget or exceed costs in general. Having an alert will notify you of approaching usage limits, I've gotten plenty of these just learning all this, as well as potential costs. I won't be going over the procedures as this is almost a one time thing when everything has been set up. Under the Billing and Cost Management Console, you are able to check, create and delete billing alerts from here.

#### AWS CLI Setup

The CLI provides direct access to the AWS public API which allows for exploring of service capabilities and develop scripts to manage resources. There is more to the CLI than this, but this is all I'm concerned about.

In order to get started, I downloaded the MSI installer directly and simply ran the installation. The MSI installed the commands/tools I need for AWS as well as Python. The installation can be verified by running "aws --version". If needed, add the installation path as an environment variable.

1) Search for Environment Variables on your computer.
2) Select Edit the system environment variables.
3) Select PATH from under User variables for <user> and click Edit.
4) Select Add and point where the AWS CLI installation was done. In my case, it was C:\Program Files\Amazon\AWSCLI.
5) Click on OK to finish up the changes.
  
The AWS CLI should now be properly set up. No more logging into the console! (Not really... I'm still going to be using it for the rest of my learning.)

## Web Hosting/EC2 Basics

#### Deploying an EC2 Instance

Now comes the fun part, actually spinning up a virtual OS instance. AWS offers a variety of Linux and Windows OS each with its own requirements and cost. As this entire lab is going to be completed on the free tier, I will be using to Amazon Linux AMI.

1) Sign into the AWS console and go to the Amazon EC2 Dashboard.
2) Select Launch Instance and choose the Amazon Linux AMI image.
3) Now, the instance type needs to be selecte. I will be using t2.micro as this is free-tier eligible.
4) Click on Review and Launch as all the other setup details can be left as default.
5) You should be on the Review Instance Launch page. Click Launch.

At this point you need to set up a key pair or use an existing one. The steps here are pretty straight-forward so I won't be going through them, but I did end up having to create a pair for myself so that I can log in. This key pair will be used to connect via a web browser or through an app like PuTTY. Once connected, I will be able to setup a web server to host a simple web page.

As most companies/corporations use Apache, I will be installing a LAMP stack on the Linux image I deployed earlier. 

> sudo yum update -y

> sudo yum install -y httpd24 php70 mysql56-server php70-mysqlnd

> sudo service httpd start

> sudo chkconfig httpd on

A security rule will be needed on the EC2 instance to allow HTTP traffic.

1) Sign into the AWS console and go to the EC2 instance that was launched.
2) Under Security Groups select View Inbound Rules.
3) Click on Add Rule and follow the chart:
    Type: HTTP
    Protocol: TCP
    Port Range: 80
    Source: Custom
4) Click on Save.

A test web page should now be accessible via the server's IP address. A new web page can be added via the /var/www/html path for Apache. As this is simply a test web page, I won't be setting up SSL/TLS.

#### Backup/Restore

The next part to test is if a backup and restore of my EC2 instance will work. Backing up/restoring is really important because if there is some sort of issue or some (big) compatability issue and you can't figure out how to fix it, restoring to a certain point is really easy. I will actually be testing that I can restore my EC2 instance because technically the restore doesn't exist if it doesn't actually work (thank you Reddit!).

1) Sign into the AWS console and go to the EC2 instance dashboard.
2) Select the EC2 instance that's going to be backed up.
3) Scroll down to the Description section at the bottom.
4) Click on the heading that says Root device and then click on the value of the EBS ID (vol-xxxxxx).
5) The volume showing up on this dashboard belongs to the EC2 instance created. Select it, if it's not already selected, and click on Actions -> Create Snapshot.
6) Enter a name and description, I used "Test Snapshot" and "Test Snapshot for restore".

At this point, a snapshot of the root device has been created. This is the most important piece needed in the restoration process. I tested the restore and the LAMP stack I set up in my initial instance carried over correctly onto that second one.

1) Under the EC2 instance dashboard, go to the Snapshot section.
2) Select the snapshot created in the above steps and go to Actions -> Create Volume.
3) Everything on this screen can be left as is. If the availability zone was changed for the instance, change it on this screen to match the instance zone.
4) Click Create.

Now that a volume has been created, another instance can be created and the newly created volume can be attached. I had to stop the instance in order to attach the volume.

1) After the instance has been stopped, go to the Volumes section and detach the volume that is already there.
2) Refresh the page, highlight the new volume and then click on Actions -> Attach Volume.
3) Select the newly created instance in order to attach the volume.
4) Click Attach.

The newly created instance is now the exact same as the first created instance. This can be verified by logging into the second instance. For me, the Apache server was still present (of course I had to reopen the HTTP port).

## Auto Scaling

## External Data/Working with Databases

## Web Hosting PaaS

## Microservices

## Serverless

## Automation

## Continuous Delivery
