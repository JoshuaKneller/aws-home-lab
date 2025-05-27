## AWS Tier 1 Walkthrough

This guide will walk through creating a basic AWS environment with a single EC2 instance inside a public subnet. This is a tier 1 of 3, designed for learning the basics of cloud engineering.

## Prerequisites
- AWS account (free tier is fine)
- Basic IT background
- Basic linux CLI skills
- Text editor basic skills (vim, nano etc)

## Step 1: Create a Virtual Private Cloud (VPC)
Creating a VPC is like creating a logically seperated network environment, within the AWS cloud.
- In the AWS dashboard, navigate to VPC>Your VPCs>Create VPC
- Name the VPC something logical "Tier1 VPC" for example.
- Fill the IPv4 block with "10.0.0.0/16"
- Leave the rest as blank and create "Create VPC"

## Step 2 - Create a Subnet
Subnets segment the VPC IP space, a /24 gives 256 IP's within the subnet, which can be used for public facing resources,ie the EC2 we will create.
- Go to Subnets>Create Subnet
- Name the subnet "Tier1-Public-Subnet"
- VPC ID select your VPC, should be named "Tier1 VPC"
- Availability Zone as something local
- IPv4 CIDR Block "10.0.1.0/24"
- Click "Create Subnet

## Step 3 - Internet Gateway
An IGW or internet gateway, is used to enable traffic from the VPC to the internet.
- Go to Internet Gateways>Create Internet Gateway
- Name the IGW "Tier1-IGW
- Click "Create"
- Select the IGW and go "Actions>Attach to VPC"
- Select "Tier1-VPC" and click "Attach"

## Step 4 - Routing Table
Routing tables allow outbound traffic from your public subnet to reach the internet, that way the traffic knows where to go.
- Go to Route Tables>Create Route Tables
- Name the table "Tier1-Public-RT"
- Select your VPC "Tier1-VPC"
- Click "Create"
- Select the route table: Routes>Edit Routes
- Add:
	- Destination as "0.0.0.0/0"
	- Target as "Internet Gateway">"Tier1-IGW"
- Click "save routes"
- Go to "Subnet associations>edit subnet Associations"
- Select "Tier-1-Public-Subnet"
- Save

## Step 5 - Create a Security Group
The security group is acts as a firewall, which allows and disallows traffic depending on how you configure it. There is other options but we will explore them at a later date.
- Go to "Security Groups>Create Security Group"
- Name it "Tier1-SG"
- Select VPC "Tier1-VPC"
- Add inbound rule;
	- Type: SSH
	- Port: 22
	- Source: 0.0.0.0/0 (or your own IP)
- Save

## Step 6 - Launch EC2 Instance
Launching the instance is like booting up the computer, the keypairs are in the place of passwords for when you SSH, protect your private key as it is literally the keys to the kingdom.
- Go to EC2>Instances>Launch Instance
- Name: Tier1-EC2
- AMI: Ubuntu 22.04 LTS (or preferred OS)
- Instance Type: t2.micro
- Key Pair: For ease we will create new
- Network Settings:
	- VPC: Tier1-VPC
	- Subnet: Tier1-Public-Subnet
	- Auto-assign public IP: Enabled
	- Security Group: Tier1-SG
- Launch the instance

## Step 7 - SSH into the EC2
- chmod 400 [KEYNAME].pem
- ssh -i [KEYNAME].pem ubuntu@public-ip-address

## You should now be in the ubuntu machine, a "sudo apt update && sudo apt upgrade -y" would be a good start.

