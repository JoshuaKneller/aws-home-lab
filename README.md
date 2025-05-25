Article Complexity;
[X] Novice
[] Apprentice
[] Adept
[] Expert
[] Master
[] Legendary

## Overview
The purpose of this project is to demonstrate multi-tier AWS network architecture with.
This includes;
- Practice AWS networking and SG management
- Understanding routing and NAT GW use cases
- Experience secure access patterns in cloud environments

The learning criteria of the project include;
- VPC with /16 CDIR block (10.0.0.0/16)
- Public and Private subnnets (/24 CIDR blocks)
- IGW attached to VPC
- NAT gateway for private subnet internet access
- EC2 instances including;
        - Public subnet (App EC2 - Ubuntu)
        - Private subnet (DB EC2 - Ubuntu server)
- Security group configuration (intra tier restrictions)
- SSH bastion access via App EC2 to DB EC2

## Usage
- SSH into App EC2 (public IP)
- From App EC2, SSH into DB EC2 (private IP)

## Future work
- Add monitoring and logging
- Implement CI/ED pipelines
- Deploy actual database and application servers

