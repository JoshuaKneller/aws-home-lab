## AWS Tier 2 Walkthrough
This guide is to expand your existing AWS VPC architecture skills by adding a private subnet and enabling internet access from private instances with a NAT gateway. 

- Note: How-to's won't be duplicated here that were mentioned in tier 1, reference tier 1 doc for refresher.

## Step 1 - Confirm tier 1 is setup correctly.
- VPC created: 10.0.0.0/16
- Public subnet: 10.0.1.0/24
- IGW attached
- Public EC2 is accessable via internet
- Public routing table has 0.0.0.0/0>IGW

## Step 2 - Create Private Subnet
- Name: Tier2-Private-Subnet
- AZ: same as public subnet
- CIDR: 10.0.2.0/24
- Choose your VPC, and AZ to same as public subnet

## Step 3 - Allocate Elastic IP
An elastic IP is a public IPv4 IP address that is issued by a CSP.
- EC2 Dashboard>Network&Security>Elastic IPs>Allocate New Address
- Make note of the IP

## Step 4 - Create NAT Gateway
The NAT gateway allows private networks to access the internet whilst preventing inbound traffic from the internet.
- VPC Dashboard>Nat Gateways>Create Nat Gateway
- Subnet: Tier1-Public-Subnet
- Attach the newly allocated elastic IP
- Name: NAT-gateway-tier2

## Step 5 - Create a private routing table
- VPC Dashboard>route tables>create route table
- Name: Private-RT-App
- Associate with your VPC
- Edit Routes;
    - Add route: 0.0.0.0/0 to NAT Gateway ID
    - Add route 10.0.0.0/16 to local (should already exist)
- Associate this route table with;
    - Subnet: Private-Subnet-App

## Step 6 - Launch EC2 in Priv Subnet
- EC2 Dashboard>Lauch instance
- Name: Private-App-Tier2
- Subnet: Tier2-Private-Subnet
- Auto-assign public IP: Disable
- Pick any OS (recommended ubuntu)
- Keypair: Select your private key (Already Created)
- SG: Create new SG called: Tier2-SG
    - Inbound: SSH FROM Tier1-SG (SG of public EP2)
    - Outbound: 0.0.0.0/0 (Default)
- Launch

## Step 7 - Check SG policies
- Tier1-SG (Public SG)
    - Inbound: SSH from your IP
    - Outbound: 0.0.0.0/0
- Tier2-SG
    - Inbound: Custom TCP>port 22>Source: Tier1-SG
    - Outbound: 0.0.0.0/0

## Step 8 - Access your Private EC2 via the Public EC2
- On your local machine, move the private key from your local machine to the public EC2:
    - scp -i path/to/publickey.pem privatekey.pem ubuntu@<Public-EC2-IP>:/path/to/wherever
- SSH to public EC2:
    - ssh -i publickey.pem ubuntu@<Public-EC2-IP>
- On the Public EC2:
    - chmod 400 privatekey.pem
    - ssh -i privatekey.pem ubuntu@<Private-EC2-IP>
- Test IP with something

## I recommend you makes notes as you go, you're a back of flesh powered by electricity from food, you'll forget stuff so write it down.

## Well done, your progress so far is;
- Tier 1: Public EC2 with internet access.
- Tier 2: Private EC2 with NAT enabled outbound access.
- Next we will do tier 3, which involves a database layer (ie private subnet, no outbound access)
    
