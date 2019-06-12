Project Overview

This is NOT a guide on how to develop websites on AWS. This uses a website as an excuse to use all the technologies AWS puts at your fingertips. The concepts you will learn going through these exercises apply all over AWS.

This guide takes you through a maturity process from the most basic webpage to an extremely cheap scalable web application. The small app you will build does not matter. It can do anything you want, just keep it simple.

Need an idea? Here: Fortune-of-the-Day - Display a random fortune each page load, have a box at the bottom and a submit button to add a new fortune to the random fortune list.

Account Basics

    Create an IAM user for your personal use.
    Set up MFA for your root user, turn off all root user API keys.
    Set up Billing Alerts for anything over a few dollars.
    Configure the AWS CLI for your user using API credentials.
    Checkpoint: You can use the AWS CLI to interrogate information about your AWS account.

Web Hosting Basics

    Deploy a EC2 VM and host a simple static "Fortune-of-the-Day Coming Soon" web page.
    Take a snapshot of your VM, delete the VM, and deploy a new one from the snapshot. Basically disk backup + disk restore.
    Checkpoint: You can view a simple HTML page served from your EC2 instance.

Auto Scaling

    Create an AMI from that VM and put it in an autoscaling group so one VM always exists.
    Put a Elastic Load Balancer infront of that VM and load balance between two Availability Zones (one EC2 in each AZ).
    Checkpoint: You can view a simple HTML page served from both of your EC2 instances. You can turn one off and your website is still accessible.

External Data

    Create a DynamoDB table and experiment with loading and retrieving data manually, then do the same via a script on your local machine.
    Refactor your static page into your Fortune-of-the-Day website (Node, PHP, Python, whatever) which reads/updates a list of fortunes in the AWS DynamoDB table. (Hint: EC2 Instance Role)
    Checkpoint: Your HA/AutoScaled website can now load/save data to a database between users and sessions

Web Hosting Platform-as-a-Service

    Retire that simple website and re-deploy it on Elastic Beanstalk.
    Create a S3 Static Website Bucket, upload some sample static pages/files/images. Add those assets to your Elastic Beanstalk website.
    Register a domain (or re-use and existing one). Set Route53 as the Nameservers and use Route53 for DNS. Make www.yourdomain.com go to your Elastic Beanstalk. Make static.yourdomain.com serve data from the S3 bucket.
    Enable SSL for your Static S3 Website. This isn't exactly trivial. (Hint: CloudFront + ACM)
    Enable SSL for your Elastic Beanstalk Website.
    Checkpoint: Your HA/AutoScaled website now serves all data over HTTPS. The same as before, except you don't have to manage the servers, web server software, website deployment, or the load balancer.

Microservices

    Refactor your EB website into ONLY providing an API. It should only have a POST/GET to update/retrieve that specific data from DynamoDB. Bonus: Make it a simple REST API. Get rid of www.yourdomain.com and serve this EB as api.yourdomain.com
    Move most of the UI piece of your EB website into your Static S3 Website and use Javascript/whatever to retrieve the data from your api.yourdomain.com URL on page load. Send data to the EB URL to have it update the DynamoDB. Get rid of static.yourdomain.com and change your S3 bucket to serve from www.yourdomain.com.
    Checkpoint: Your EB deployment is now only a structured way to retrieve data from your database. All of your UI and application logic is served from the S3 Bucket (via CloudFront). You can support many more users since you're no longer using expensive servers to serve your website's static data.

Serverless

    Write a AWS Lambda function to email you a list of all of the Fortunes in the DynamoDB table every night. Implement Least Privilege security for the Lambda Role. (Hint: Lambda using Python 3, Boto3, Amazon SES, scheduled with CloudWatch)
    Refactor the above app into a Serverless app. This is where it get's a little more abstract and you'll have to do a lot of research, experimentation on your own.
        The architecture: Static S3 Website Front-End calls API Gateway which executes a Lambda Function which reads/updates data in the DyanmoDB table.
        Use your SSL enabled bucket as the primary domain landing page with static content.
        Create an AWS API Gateway, use it to forward HTTP requests to an AWS Lambda function that queries the same data from DynamoDB as your EB Microservice.
        Your S3 static content should make Javascript calls to the API Gateway and then update the page with the retrieved data.
        Once you have the "Get Fortune" API Gateway + Lambda working, do the "New Fortune" API.
    Checkpoint: Your API Gateway and S3 Bucket are fronted by CloudFront with SSL. You have no EC2 instances deployed. All work is done by AWS services and billed as consumed.

Cost Analysis

    Explore the AWS pricing models and see how pricing is structured for the services you've used.
    Answer the following for each of the main architectures you built:
        Roughly how much would this have costed for a month?
        How would I scale this architecture and how would my costs change?
    Architectures
        Basic Web Hosting: HA EC2 Instances Serving Static Web Page behind ELB
        Microservices: Elastic Beanstalk SSL Website for only API + S3 Static Website for all static content + DynamoDB Table + Route53 + CloudFront SSL
        Serverless: Serverless Website using API Gateway + Lambda Functions + DynamoDB + Route53 + CloudFront SSL + S3 Static Website for all static content

Automation

!!! This is REALLY important !!!

    These technologies are the most powerful when they're automated. You can make a Development environment in minutes and experiment and throw it away without a thought. This stuff isn't easy, but it's where the really skilled people excel.
    Automate the deployment of the architectures above. Use whatever tool you want. The popular ones are AWS CloudFormation or Teraform. Store your code in AWS CodeCommit or on GitHub. Yes, you can automate the deployment of ALL of the above with native AWS tools.
    I suggest when you get each app-related section of the done by hand you go back and automate the provisioning of the infrastructure. For example, automate the provisioning of your EC2 instance. Automate the creation of your S3 Bucket with Static Website Hosting enabled, etc. This is not easy, but it is very rewarding when you see it work.

Continuous Delivery

    As you become more familiar with Automating deployments you should explore and implement a Continuous Delivery pipeline.
    Develop a CI/CD pipeline to automatically update a dev deployment of your infrastructure when new code is published, and then build a workflow to update the production version if approved. Travis CI is a decent SaaS tool, Jenkins has a huge following too, if you want to stick with AWS-specific technologies you'll be looking at CodePipeline.

Miscellaneous / Bonus

These didn't fit in nicely anywhere but are important AWS topics you should also explore:

    IAM: You should really learn how to create complex IAM Policies. You would have had to do basic roles+policies for for the EC2 Instance Role and Lambda Execution Role, but there are many advanced features.
    Networking: Create a new VPC from scratch with multiple subnets (you'll learn a LOT of networking concepts), once that is working create another VPC and peer them together. Get a VM in each subnet to talk to eachother using only their private IP addresses.
    KMS: Go back and redo the early EC2 instance goals but enable encryption on the disk volumes. Learn how to encrypt an AMI.


___________________________________________

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
