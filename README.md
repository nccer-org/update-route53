update-route53
======
Script to update AWS Route 53 record set on startup of Lightsail instance.

The public IP address given to a Lightsail instance changes after an instance stops and starts again. This causes any Route53 recordsets to become instantly outdated. An easy fix is to use (VPC) Elastic IPs, which stick with the EC2 after a restart; however, [you can only have 5 per region](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/elastic-ip-addresses-eip.html#using-instance-addressing-limit) and need a good excuse when asking Amazon to increase it.
 
### Table of Contents
&nbsp;&nbsp;[Pre-requisites](#1-pre-requisites)  
&nbsp;&nbsp;&nbsp;&nbsp;[IAM Role](#create-aws-iam-role)  
&nbsp;&nbsp;&nbsp;&nbsp;[AWS CLI](#install-the-aws-command-line-interface-aws-cli)  
&nbsp;&nbsp;[Download the Script](#2-download-the-script)  
&nbsp;&nbsp;[Update Script Variables](#3-update-script-aws-variables)  
&nbsp;&nbsp;[Set Script Permissions](#4-set-script-permissions)  
&nbsp;&nbsp;[Add to Runlevels](#5-add-to-runlevels)  
&nbsp;&nbsp;[References](#references)


## 1. Pre-requisites

###  Create AWS IAM Role.
Your EC2 instance will need permissions to update a Route53 recordset. To avoid storing keys on the EC2 instance, you will setup a new role in IAM and attach it to your EC2 at launch. (We'll use  the console to create the role.)

  * Within IAM's navigation pane, click on 'Roles.'  
  * Click the 'Create New Role' button.  
![Create New Role Button](/../readme-images/images/1-create-new-role.png?raw=true "Create New Role")
<br />

  * Name your new role. I use `route53-editor`. Click Next Step.  
![Set Role Name](/../readme-images/images/2-set-role-name.png?raw=true "Set Role Name")
<br />

  * Select the `Amazon EC2` service role, under the `AWS Service Roles` section.  
![Select Role Type](/../readme-images/images/3-select-role-type.png?raw=true "Select Role Type")
<br />

  * Attach a policy. In the filter, type `route53`. Choose the `AmazonRoute53FullAccess` policy and click Next Step.  
![Attach Policy](/../readme-images/images/4-attach-policy.png?raw=true "Attach Policy")
<br />

  * Review your settings on the next page, and if correct, click the Create Role button.  
  
  * Use this new role when launching your EC2 instances.  
  >Note: If you have an existing role that you need to use, just attach the Route53 policy to your existing role.
<br />

### Install the AWS Command Line Interface (AWS-CLI)

Install the AWS CLI 
```curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip" 
unzip awscliv2.zip
sudo ./aws/install 
```
<br />
Configure the AWS CLI using sudo so that the configuration applies to root. 
```sudo aws configure```
TODO: Add instructions for service-linked role configuration (https://lightsail.aws.amazon.com/ls/docs/en_us/articles/amazon-lightsail-using-service-linked-roles)

## 2. Download the Script
Download the script into your `/etc/init.d` directory.
```bash
 sudo curl --location "https://raw.githubusercontent.com/nccer-org/update-route53/master/update-route53.sh" --output /etc/init.d/update-route53.sh 
```
<br /><br />

## 3. Update Script AWS Variables
Update the `ZONEID` and `RECORDSET` variables in the script to reflect the Zone and Route53 record you want to change.
<br /><br />

## 4. Set Script Permissions
Give the script execute permissions.
```bash
sudo chmod +x /etc/init.d/update-route53.sh
```
<br />

## 5. Add to Runlevels
Add the script to the default runlevels so it will be called at runtime.
```bash
sudo update-rc.d update-route53.sh defaults
```
>Note: To remove the script from runlevels...`sudo update-rc.d /etc/init.d/update-route53.sh remove`
<br />

### References
Creating the script:  
  - http://www.spidersoft.com.au/2016/how-schedule-start-and-stop-ec2-instance-on-aws/  

Running script at startup:  
  - http://xmodulo.com/how-to-automatically-start-program-on-boot-in-debian.html  
  - https://www.cyberciti.biz/tips/linux-how-to-run-a-command-when-boots-up.html  
  - http://askubuntu.com/questions/409025/permission-denied-when-running-sh-scripts  

AWS Command Line Interface Installation:
  - http://docs.aws.amazon.com/cli/latest/userguide/aws-cli.pdf  
  - http://docs.aws.amazon.com/cli/latest/userguide/awscli-install-linux.html  

AWS IAM Policies:  
  - http://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies.html  
  
  
