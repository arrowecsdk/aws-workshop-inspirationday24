# AWS Workshop Inspiration Day 2024

## Agenda

- Create DynamoDB
- Create Policy and Role for EC2 to access DynamoDB and Session Manager
- Create VPC
- Create

## Config

### DynamoDB

Go to: DynamoDB

Click: Create table

Table name: noteapp-table

partition Key: id

Type: String

Table Settings:

Select: Customize setttings

Select: DynamoDB Standard

Select: On-Demand

Leave the rest as default

Click: Create Table

We need the ARN (Amazon Resource Name)

Click on the new table: noteapp-table

Click: Additional info

The Amazon Resource Name (ARN) is in the last part of the section

Copy the ARN and save it in a notepad

### IAM Policy and Role

Go to: IAM

In the left menu select: Policies

Click: Create Policy

Set Policy editor to: JSON

Copy below to the Policy editor:

And change the "Resource" to the ARN you saved before

```JSON
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "dynamodb:PutItem",
                "dynamodb:GetItem",
                "dynamodb:UpdateItem",
                "dynamodb:DeleteItem",
                "dynamodb:Scan"
            ],
            "Resource": "arn:aws:dynamodb:YOUR_REGION:YOUR_ACCOUNT_ID:table/YOUR_TABLE_NAME"
        }
    ]
}

```

Click: Next

Name: noteapp-table-Policy

Click: Create Policy

In the left menu Select: Roles

Click: Create Role

Trusted entity type: AWS Service

Use Case: EC2

Click: Next

Permissions Policies:

Search for and select:

- noteapp-table-Policy
- AmazonSSMManagedInstanceCore

Click: Next

Rolename: noteapp-ec2

Click: Create Role

### VPC

Go to: VPC

Click: Create VPC

Use: VPC and more

Auto-generate: nodeapp

IPv4 CIDR Block: 10.0.0.0/16

Number of Availability Zones: 2

Number of Public Subnet: 2

Number of Private Subnet: 2

No NAT Gateway

No VPC Endpoints

Click: Create VPC

Click: View VPC

### Security Group

Create three Security Groups

In the left menu click: Security Groups

#### First

Click "Create Security Group"

Name: noteapp-natinstance

Description: Allow localtraffic

Select VPC: noteapp-vpc

Click Add in Inbound rules

Type: All Traffic Source: Custom 10.0.0.0/16

Click "Create Security Group"

#### Second

Click "Create Security Group"

name: noteapp-lb

Description: Allow http from anywhere

Select VPC: noteapp-vpc

Click Add in Inbound rules

Type: HTTP Source: Custom 0.0.0.0/0

Click "Create Security Group"

#### Third

Click "Create Security Group"

name: noteapp

Description: Allow http from loadbalancer

Select VPC: noteapp-vpc

Click Add in Inbound rules

Type: HTTP Source: Custom: Select the security Group: noteapp-lb

Click Add in Inbound rules

Type: HTTPS Source: Custom: 0.0.0.0/0

Click Add in Inbound rules

Type: SSH Source: Custom: 0.0.0.0/0

Click "Create Security Group"

### VPC Endpoints SSM

We need three Interface Endpoints for Session Manager

In the left menu go to: Endpoints

Click: Create Endpoint

Name: ssm

Type: AWS service

In services Search for: ssm

Select: com.amazonaws.eu-central-1.ssm

VPC: noteapp-vpc

Subnet: Select the two Availability Zones and select the private subnet in each

Security Group: noteapp

Policy: Full access

Click: Create endpoint

Create two more for the folling Endpoints

- ssmmessages
- ec2messages

### VPC Endpoint DynamoDB

Click: Create Endpoint

Name: DynamoDB

AWS service

Search: DynamoDB

Select the Gateway service

VPC: noteapp-vpc

Route Tables:

Select the two route tables:
¨

- noteapp-rtb-private2-eu-north-1b
- noteapp-rtb-private1-eu-north-1a

Policy: Full access

Click: Create endpoint

### EC2 Nat Instance

Create a Nat Instance

Go to: EC2

Click: Launch Instance

Name: NatInstance

Application and OS Images:

OS: Amazon Linux

AMI: Amazon Linux 2023 AMI

Architecture: 64bit (x86)

Instance Type: t3.micro 1 core 1 Gb Mem

Keypair: Proceed without a key pair

Network Settings: Click Edit

VPC: Select noteapp-VPC

Subnet: Select noteapp-public-1

Autoassign public IP: Enable

Select existing Security Group: noteapp-natinstance

Open Advanced details:

In User data: Add script

```bash
#!/bin/bash
sudo yum update && sudo yum upgrade -y
sudo yum install iptables-services -y
sudo systemctl enable iptables
sudo systemctl start iptables
echo net.ipv4.ip_forward=1 | sudo tee -a /etc/sysctl.d/custom-ip-forwarding.conf
sudo sysctl -p /etc/sysctl.d/custom-ip-forwarding.conf
sudo /sbin/iptables -t nat -A POSTROUTING -o ens5 -j MASQUERADE
sudo /sbin/iptables -F FORWARD
sudo service iptables save

```

Select the instance: NatInstance

Click: Actions / Networking / Change Source/Destination check

Select: Stop

Click: Save

### VPC Route Tables

Go to: VPC

In the left menu Click: Route Tables

For both private Route Tables:

- noteapp-rtb-private2-eu-north-1b
- noteapp-rtb-private1-eu-north-1a

Click: Edit Routes

Add route:

Destination: 0.0.0.0/0

Target: Select Instance

Select: Natinstance

Click: Save Changes

### EC2

Go to: EC2

Click: Launch Instance

Name: Host1

Application and OS Images:

OS: Amazon Linux

AMI: Amazon Linux 2023 AMI

Architecture: 64bit (Arm)

Instance Type: t3.micro 1 core 1 Gb Mem

Keypair: Proceed without a key pair

Network Settings: Click Edit

VPC: Select noteapp-VPC

Subnet: Select noteapp-private-1

Autoassign public IP: Disable

Select existing Security Group: noteapp

Open Advanced details:

In IAM instance profile Select: noteapp-ec2

Click: Launch Instance

### Configure EC2

Select the instance: Host1

At the top Click: Connect

Select: Session Manager

Click: Connect

Test table access

Run command:

```bash

aws dynamodb scan --table-name noteapp-table

```

The Result looks like this, if there is connection:

```bash
{
    "Items": [],
    "Count": 0,
    "ScannedCount": 0,
    "ConsumedCapacity": null
}

```

Install NodeJS and Website

```bash

sudo su ec2-user

cd /home/ec2-user

sudo dnf update -y

sudo dnf install nodejs -y

node --version
npm --version

mkdir note-app
cd note-app
npm init -y

npm install express aws-sdk body-parser

mkdir public

sudo npm install -g pm2

curl 'https://raw.githubusercontent.com/arrowecsdk/aws-workshop-inspirationday24/refs/heads/main/noteapp/server.js' > server.js

curl 'https://raw.githubusercontent.com/arrowecsdk/aws-workshop-inspirationday24/refs/heads/main/noteapp/public/index.html' > public/index.html

pm2 start server.js

pm2 status

pm2 startup

# Run the command that PM2 gives you
# Like this
# sudo env PATH=$PATH:/usr/bin /usr/local/lib/node_modules/pm2/bin/pm2 startup systemd -u ec2-user --hp /home/ec2-user

pm2 save

```

Install Nginx

```bash
sudo dnf install nginx -y

sudo systemctl start nginx
sudo systemctl enable nginx

sudo tee -a /etc/nginx/conf.d/note-app.conf <<'EOF'
server {
    listen 80;
    server_name _;  # Replace with your domain if you have one

    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
EOF

sudo nginx -t

sudo systemctl restart nginx

```

### EC2 Instance Host2

Create the second host

Launch Instance

Host1
Amazon Linux
64bit ARM
t4g.micro 2 core 1 Gb Mem
No keypair
Network
    - noteapp-VPC
    - subnet noteapp-public-1
    - autoassign public IP
    - select existing Security Group noteapp-public

Advanced details
    - IAM instance profile: noteapp-ec2
    - User data: add script

```bash
#!/bin/bash
dnf update -y
dnf install nodejs -y
cd /home/ec2-user
mkdir -p note-app/public
chown -R ec2-user:ec2-user note-app
cd note-app
sudo -u ec2-user npm init -y
sudo -u ec2-user npm install express aws-sdk body-parser
npm install -g pm2
sudo -u ec2-user curl 'https://raw.githubusercontent.com/arrowecsdk/aws-workshop-inspirationday24/refs/heads/main/noteapp/server.js' > /home/ec2-user/note-app/server.js
sudo -u ec2-user curl 'https://raw.githubusercontent.com/arrowecsdk/aws-workshop-inspirationday24/refs/heads/main/noteapp/public/index.html' > /home/ec2-user/note-app/public/index.html
chown -R ec2-user:ec2-user /home/ec2-user/note-app
cd /home/ec2-user/note-app
sudo -u ec2-user PM2_HOME=/home/ec2-user/.pm2 pm2 start server.js
sudo -u ec2-user PM2_HOME=/home/ec2-user/.pm2 pm2 startup systemd -u ec2-user --hp /home/ec2-user
sudo -u ec2-user PM2_HOME=/home/ec2-user/.pm2 pm2 save
dnf install nginx -y
cat > /etc/nginx/conf.d/note-app.conf << 'EOF'
server {
    listen 80;
    server_name _;

    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
EOF
rm -f /etc/nginx/conf.d/default.conf
systemctl start nginx
systemctl enable nginx
```

### Load Balancer - security group

Name: noteapp-lb

Description: Allow http

VPC: noteapp-vpc

Inbound Rules: HTTP from 0.0.0.0/0s

### Load Balancer - Target Group

Create Target Group

Target Type: Instances

Name: noteapp-Target

VPN: noteapp-vpc

Select Nodes: host1 and host2

Add as pending below

### Load Balancer - Application Load Balancer

Create Application Load Balancer

name: noteapp-lb

Schema: Internet facing

Network mapping: noteapp-vpc
