# CSDO1010-007-B-W24-LAB3

## Lab 3 - Manage AWS Infrastructure with Terraform (IaC) and Ansible (CM)

## Lab Evidence

Your report on this lab will be the basis for your grade.  The purpose of the report is to demonstrate to your instructor that you have completed the lab successfully and understood the material.  
Please include the following screenshots as part of your evidence.

- `webserver-Public-URL` and the `URL` assigned from the end of the Terraform apply output.
  - Please do not include all of the output from apply, just the `webserver-Public-URL` value
- The Cloud Computing Course webpage in your browser, including the address bar showing the URL
- Provisioned Infrastructure as viewed through your AWS account, including the public IP of your `webserver`, under Instances
- `ls` command output from `putty` session to your EC2 instance to show folder contents.

A portion of the grade will be allocated to the quality of the report.  The report should be well formatted, concise and to the point.  

## Learning Goals

Demonstrate and execute steps for managing AWS Infrastructure with Terraform (IaC) and Ansible (CM).

## Pre-requisite

- Created AWS free tier accounts account (Check provided AWS instructions in Moodle - 02.Create AWS Free Tier Account.docx)
- Completed the following in Lab2: Manage AWS Infrastructure with Terraform
- Downloaded and installed Terraform on your local machine link
- Created AWS free tier accounts accounthttps://github.com/
- Downloaded and installed AWS CLI on your local machine link
- Configured AWS Access Keys

## Introduction

You study during this week how Configuration Management (CM) and how you can use Ansible as CM tool. Let's work through a scenario which combine what you have learned in the previous week and this week to manage AWS infrastructure with Terraform (IaC) and Ansible (CM).

### Demonstrate Steps for managing AWS Infrastructure with Terraform (IaC) and Ansible (CM)

To accomplish this, you are going to follow these steps:

1. Generate SSH Keys
2. Download PuTTY
3. Download PuTTYgen and Generate .ppk key
4. Terraform Structure
5. Terraform Code
6. Ansible Play
7. Terraform and Ansible in action
8. Connect to EC2 instance using PuTTY
9. Terraform destroy

Step 1: Generate SSH Keys
This will be used later to connect to EC2 instances in your code and using ssh terminal
Open a terminal window. At the shell prompt, type the following command:
ssh-keygen -t rsa
The ssh-keygen program will prompt you for the location of the key file. Press Return to accept the defaults.
Press enters twice for no passphrase
Note the location of your public and private keys were saved, they will be required in a subsequent steps

Here are the generated keys
id_rsa is your private key
id_rsa.pub is your public key

Steps 2 & 3 provide instructions for downloading and installing Putty, which allows you to connect to a remove server, an AWS EC2 instance in the case of this lab.  
If you are using Mac OS, then you can connect to a remote server directly from the terminal window - you do not need to complete steps 2 & 3.

Step 2: Download PuTTY
PuTTY website link
Download PuTTY link
More details on that later in the document when you connect to EC2 instance. For now, just download it.

Step 3: Download PuTTYgen and Generate .ppk key
PuTTYgen will be used to convert .pem file into a .ppk to connect to EC2 instance using PuTTY
PuTTYgen website link (Check the version for your OS)
For more information on related subject you may refer to How do I convert a .pem file into a .ppk, and vice versa, on Windows and Linux? link
Here is a demonstration for Windows OS (64-bit x86)
Download (a RSA and DSA key generation utility) - 64-bit x86: puttygen.exe

Click on Load and select all files

Select your private key id_rsa

Now click save private key -> click Yes

Name your key and note where you will save it

You should end up with three generated keys
id_rsa is your private key
id_rsa.pub is your public key
myKey.ppk is you PuTTY private key

Step 4: Terraform Structure
The same structure as the previous module, with one addition the Ansible play (install_apache.yaml)

Step 5: Terraform Code
In this part will go only through the modifications of Terraform Code from the previous module
Terraform Main
Same code from the previous module, no changes
Terraform Virtual Private Cloud (VPC) Module
Same code from the previous module, no changes
Terraform Compute (EC2) Module
outputs.tf same code from the previous module, no changes
main.tf & variables.tf modification (Understand Terraform Code)
Here we create a key pair in AWS using the public key we generated in a previous step
file is a function that reads the content in a file at the given path and returns them as a string, we are passing it a variable which declared in variables.tf

Here is the ssh_key_public variable. You must change the path of the public key file (id_rsa.pub) to the location you noted earlier

key_name = aws_key_pair.aws-key.key_name this will add the public key inside aws_instance resource

Then we are connecting to the create EC2 instance using SSH,
The default user for connecting to EC2 instance is ec2-user
private_key   = file(var.ssh_key_private) here we are connecting to the EC2 instance using the private key (Never share your private key with anyone - These keys in a real-world scenario will be stored in a key vault)
Here is the ssh_key_private variable. You must change the path of the private key file (id_rsa) to the location you noted earlier
host = self.public_ip interpolate that resource's public IP address.

Here is the ssh_key_private variable. You must change the path of the public key file (id_rsa) to the location you noted earlier

The file provisioner used to copy the ansible yaml file (install_apache.yaml) from your local machine to the newly create resource in that case EC2 instance

Then we are executing a script on the created resource remotely (EC2 instance)
The remote-exec provisioner invokes a script on a remote resource after it is created.
The inline execute a list of command stings in the provided order. Basically, here we are updating the linux distro, installing ansible in order to be able to execute the ansible play on the EC2 instance.
Then wait for 60 seconds and then execute the ansible playbook by running the command ansible-playbook install_apache.yaml

Step 6: Ansible Playbook
Understanding Ansible
The three --- tell ansible this is the start of a playbook
name is the name of your play; you can put the name you want
hosts: localhost specify which host you want this play on, in this case it is the localhost
remote_user is the instance user in case of EC2 it is ec2-user
become: true allows you to escalate privileges and become another user other than the user logged into the machine (ec2-user). The purpose of this in our case to be able to install Apache.

We will break the construction of our playbook into three parts package management, configuration, and service handlers
Package Management
Here we are installing the packages required by our system
tasks a list of task, a task is the smallest unit of action you can automate using ansible playbook. Ansible executes tasks in the same order they a re defined inside a playbook
Then you name your tasks whatever you want to name it. It is optional but make it easier to read the outputs of playbook
We are installing the Apache package that we will need to run the website using the built in module offered by ansible. The version we are installing it is the latest version of Apache
    yum: (package manager module)
      name: httpd (Apache)
      state: latest (latest version)

Infrastructure Configuration
Here we are configuring our system with the necessary application or configuration files that we need to configure the environment
The unarchive module unpacks an archive. It will copy the files from the source to the destination before unpacking it
remote_src: yes it will go to the remote/target machine for the source

The copy module copies a file from the local or remote machine (source) to a location on the remote machines (destination). Here we copying the unpacked archive contents from the tmp to Apache location to run the website

Ansible modules check whether the desired final state has already been achieved. This ensure that Apache is running and it is state started

Service Handlers
Create service handlers to start, stop, or restart our system. Sometimes you want a task to run only when a change is made on a machine. For example, you may want to restart a service if a task updates the configuration of that service, but not if the configuration is unchanged. Ansible uses handlers to address this use case.

Step 7: Terraform and Ansible in action
terraform init: Locate your working directory (Lab3-Ansible) and initialize terraform terraform init

terraform validate: Validate your terraform

terraform apply: Provision terraform managed infrastructure. You must confirm by tying yes if you would like to continue and perform the actions described to provision your infrastructure resources

Watch the ansible playbook execution

Now you can access the website from the Cloud Computing course using the URL provided in the output

You can also login to your AWS account and see the provisioned infrastructure resources. You must select N.Virginia which is us-east-1 from the top right corner

Step 8: Connect to EC2 instance
Using Mac OS - Terminal
Using the Public Address you got in the output, enter the following command from a terminal window.

ssh ec2-user@184.72.67.219

Using PuTTY
Here is a demonstration for connecting to the EC2 instance from Windows OS machine
Download putty.exe (the SSH and Telnet client itself) - 64-bit x86: putty.exe
Open PuTTy
Put the Public Address you got in the output

Then go to Auth under SSH

Browse to the location of your myKey.ppk generated early using PuTTYgen

Go back to the Session and click Open to connect to your EC2 instance

Click yes

User to login ec2-user.

Step 9: Terraform destroy
Once you are done with the lab you must destroy the provisioned infrastructure resources to avoid any risks of encountering cost in the future.
terraform destroy: Destroy terraform managed infrastructure. You must confirm by tying yes

Note
Do not delete the keys you generated on your local machines. You will need it for subsequent labs.
Conclusion
Congrats on creating your Infrastructure using Terraform (IaC) and Ansible (CM)!
