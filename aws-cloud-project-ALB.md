## Step 1 - Create a Target Group

- AWS Console > EC2 > Target Groups > Create Target Group
- Target type: Instances
- Protocol: HTTP
- Port: 80
- VPC: Use existing (10.0.0.0/16)
- Name: tier4-target-group
- Health check path: '/' (default is fine)
- Register targets: Register your private EC2 instance(s)
- Click Create

## Step 2 - Create a Second Public Subnet

- AWS Console > VPC > Subnets > Create Subnet
- Name: public-subnet-2
- VPC: Select your existing VPC (10.0.0.0/16)
- Availability Zone: Choose a different AZ than your first public subnet (e.g. ap-southeast-2b if first was 2a)
    - ALBs require subnets in at least two AZs for high availability
- CIDR block: 10.0.4.0/24
- Click Create
- Go to Route Table Associations and associate it with your public route table (the one with 0.0.0.0/0 -> IGW)

## Step 3 - Create a Security Group for the ALB

- AWS Console > VPC > Security Groups > Create Security Group
- Name: tier4-alb-sg
- VPC: Select current VPC
- Inbound rules:
    - HTTP (80) from 0.0.0.0/0
    - (Optional: HTTPS (443) from 0.0.0.0/0 if you plan to use SSL later)
- Outbound rules:
    - All traffic (default 0.0.0.0/0 is fine)
- Click Create Security Group

## Step 4 - Create the Application Load Balancer

- AWS Console > EC2 > Load Balancers > Create Load Balancer > Application Load Balancer
- Name: tier4-alb
- Scheme: Internet-facing
- IP address type: IPv4
- Listener: HTTP (Port 80)
- Availability Zones: Select two AZs (e.g. ap-southeast-2a and 2b)
- Subnets: Select both public subnets (e.g. 10.0.1.0/24 and 10.0.4.0/24)
- Security Group: tier4-alb-sg
- Forwarding Target Group: tier4-target-group
- Click Create

## Step 5 - Update Security Group on Private EC2

- AWS Console > EC2 > Instances > Select private EC2
- Actions > Networking > Change Security Groups
- Edit the security group assigned to the private EC2 (e.g. private-ec2-sg)
- Add inbound rule:
    - Type: HTTP
    - Port: 80
    - Source: Custom - select the SG ID of tier4-alb-sg
- Click Save Rules

## Step 6 - Test the ALB

- Go to EC2 > Load Balancers > tier4-alb
- Copy the DNS name listed in the description
- Paste it into your browser
- You should see the default Nginx welcome page (or your app response)

## TLDR – ALB with Private EC2s + SSM Access

- Create target group (HTTP, port 80, instance target type, VPC = 10.0.0.0/16)
- Register private EC2 instances
- Create second public subnet in a different AZ (10.0.4.0/24), attach to public route table
- Create ALB security group (allow HTTP 80 from 0.0.0.0/0)
- Create IAM role with AmazonSSMManagedInstanceCore
- Launch 2x EC2 in private subnets:
    - Ubuntu 22.04
    - Attach SSM IAM role
    - No public IP
    - Install nginx via user data
    - Use SG that allows HTTP from ALB SG only
- Confirm EC2s are "SSM Managed"
- Start SSM Session, verify with: curl localhost
- Create ALB:
    - Internet-facing
    - Listener: HTTP 80
    - AZs: 2 (public-subnet-1 + public-subnet-2)
    - SG: ALB SG
    - Forward to: your target group
- Update EC2 SG to allow HTTP from ALB SG
- Test ALB DNS in browser

