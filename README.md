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

###  IAM Role already created with permissions to update Route53.
We are using `DNSManagers_NCCER`.
We have three NCCER policies, one for each domain in Route53. BYF domain is separate.
<br />

### AWS Command Line Interface is already installed
Install the AWS CLI 
```curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip" 
unzip awscliv2.zip
sudo ./aws/install 
```
<br />
### AWS ClI is already correctly configured
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
### Testing Functionality
IP addresses dont usually change when you reboot. To force an IP change on an existing instance, stop the instance, wait until it has completely shut down, then start it again. This will cause a new IP to be applied, and the script will update DNS. 

If something doesn't work, check the log file as configured 

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
  
  
