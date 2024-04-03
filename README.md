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
