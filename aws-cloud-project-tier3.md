## AWS Tier 3 Walkthrough
This guide will expand your AWS VPC architecture by adding a 3rd tier (database tier) with no internet access. After this guide you will be able to construct a basic 3 tier cloud environment (Public>Private>Database).

- Note: How-to's won't be duplicated here that were mentioned in tier 1&2, reference tier 1&2 doc for refresher.

## Step 1 - Create the Private Subnet for DB tier.
- Name: tier3-private-subnet
- AZ: same as others (something local)
- CIDR: 10.0.3.0/24
- VPC: use existing VPC (10.0.0.0/16)

## Step 2 - Create Security Group
- Name: tier3-SG
- Inbound:
    - MySQL/Aurora (TCP:3306) from tier2-SG (App EC2 SG)
- Outbound:
    - Leave as default (0.0.0.0/0).

## Step 3 - Private Route Table
- Name: Private-RT-db
- Routes:
    - local: 10.0.0.0/16 (local)
    - **NO 0.0.0.0/0** (allows outbound traffis but no internet access due to routing)

## Step 4 - Launch EC2
- Name: Tier3-EC2-db
- Subnet: tier3-private-subnet
- Auto-assign Public IP: disable
- OS: ubuntu (or whatever you want for a db os)
- Keypair: use **SAME KEY** as for tier2 (private key)
- SG: tier3-SG

## Step 5 - Copy Private Key
**Note: in tier 2 we moved the private key from our local machine into the public ec2, and used it to access the private ec2, we now need to copy the private key from public to private ec2's, so that once we are inside the private ec2, we can use it to move into the db ec2.**
- SSH into the public EC2, then;
    - chmod 400 privatekey.pem
    - scp -i privatekey.pem privatekey.pem ubuntu@<EC2-Private-IP>:/home/ubuntu/privatekey1.pem
**It should be noted we are using the same key to authenticate and also trasfering it, as this is a home lab this is fine but best practice is to avoid sharing and reusing private keys in prod**

## Summary
- You should now be able to ssh from Public>Private>DB
- If all is done correctly, the DB will have no internet access or public IP, and can only be reached by the app tier EC2.

## Build Summary
- Tier 1 (Public Subnet): Bastion Host/Jump Box, public facing
- Tier 2 (Private Subnet): App Server, NAT Access for updates
- Tier 3 (Private Subnet): Database Server, fully isolated
