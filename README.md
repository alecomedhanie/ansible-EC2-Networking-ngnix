

Ansible Multi-OS Web Server Deployment with AWS VPC Setup

This project is an Ansible playbook to automate the setup and deployment of a web server across multiple EC2 instances with different Linux distributions (e.g., RedHat, Debian, Amazon Linux) in a secure AWS Virtual Private Cloud (VPC). This setup involves provisioning an isolated VPC, setting up subnets, configuring security groups, deploying NGINX, and uploading a custom index.html file to each instance.

Table of Contents

	•	Project Overview
	•	Prerequisites
	•	Project Structure
	•	Setup
	•	Usage
	•	License

Project Overview

This Ansible project automates the following:

	1.	Provisioning AWS Infrastructure:
	•	Creates a custom VPC with subnets, an internet gateway, and a route table.
	•	Sets up security groups for SSH and HTTP access to the EC2 instances.
	2.	Deploying Web Servers:
	•	Provisions multiple EC2 instances across different Linux distributions.
	•	Installs and configures NGINX on each server.
	3.	Customizing Web Content:
	•	Deploys a custom index.html to each server’s NGINX directory.
	•	Configures each instance to serve the HTML content via HTTP.

Prerequisites

Ensure you have:

	•	Ansible installed on your local machine
	•	AWS Access Keys with permissions for EC2, VPC, and related services
	•	SSH Key for EC2 access (e.g., ansible.pem)
	•	Ansible Vault for encrypted credentials if needed
	•	Python Boto3 library installed for AWS dynamic inventory management


Project Structure

![Screenshot 2024-10-31 at 4 26 00 PM](https://github.com/user-attachments/assets/0fdf8720-4a6d-4866-8d46-671b7cc4fb59)


Setup

Step 1: Configure ansible.cfg

Ensure ansible.cfg has the necessary configurations:

[defaults]
inventory = aws_ec2.yml
host_key_checking = False

Step 2: Set Up AWS Dynamic Inventory (aws_ec2.yml)

Define the dynamic inventory configuration with aws_ec2 plugin and filters for instances with specific tags:

plugin: amazon.aws.aws_ec2
regions:
  - us-east-1  # Update to match your region
filters:
  "tag:Name": "webserver*"
hostnames:
  - tag:Name
compose:
  ansible_host: public_ip_address
  ansible_user: ec2-user

Step 3: Place the index.html File in the files Directory

Modify index.html as needed for your web server content.

Usage

Step 1: Run the VPC Setup Playbook

	1.	Run the following playbook to create the VPC and associated resources:

ansible-playbook -i aws_ec2.yml create_vpc.yml --vault-password-file vault.pass --private-key ~/Downloads/ansible.pem -u ec2-user

This will create:
	•	A VPC with a custom CIDR range.
	•	A subnet within the VPC.
	•	An internet gateway and route table for outbound internet access.
	•	A security group with rules to allow inbound SSH and HTTP access and unrestricted outbound access.

	2.	Provision EC2 Instances:
	•	The instances are created within the VPC and associated subnet.

Step 2: Deploy NGINX on EC2 Instances

Run the playbook to install NGINX across all EC2 instances:

ansible-playbook -i aws_ec2.yml install_nginx.yml --vault-password-file vault.pass --private-key ~/Downloads/ansible.pem -u ec2-user

Step 3: Upload the HTML File

Run the playbook to copy index.html to each EC2 instance:

ansible-playbook -i aws_ec2.yml upload_index.yml --vault-password-file vault.pass --private-key ~/Downloads/ansible.pem -u ec2-user

Step 4: Access the Web Servers

Once the playbooks are complete, open each server’s public IP in a browser to view the deployed index.html.


