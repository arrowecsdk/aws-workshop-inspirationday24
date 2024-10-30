# AWS Workshop Inspiration Day 2024

## Agenda

- Create DynamoDB
- Create Policy for DynamoDB
- Create VPC
- Create

## Config

### DynamoDB

Create table

noteapp-table

partition Key: id

Table Settings:

Customize
DynamoDB Standard
On-Demand

### IAM Policy and Role

IAM / Policy

Create Policy

JSON

Get the Table arn

DynamoDB / Tables / noteapp-table

Overview / Additional info

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

Name: noteapp-table-Policy

IAM / Roles

Create Role

EC2

Permissions:

- noteapp-table-Policy
- AmazonSSMManagedInstanceCore

Rolename:
noteapp-ec2

### VPC

- public subnet 1
- public subnet 2
- private subnet 1
- private subnet 2
- security group
- route table

Create VPC

VPC and More

auto-generate
nodeapp

cidr
10.0.0.0/16

2 Availability Zones

2 Public Subnet

2 Private Subnet

No NAT Gateway

No VPC Endpoints

### Security Group

Create three Security Groups

Go to VPC / Security Groups

#### First

Click "Create Security Group"

name: noteapp-natinstance

Description: Allow localtraffic

Select VPC: noteapp-vpc

Inbound rule:

Add rule:

Type: All Traffic Source: Custom 10.0.0.0/16

Click "Create Security Group"

#### Second

Click "Create Security Group"

name: noteapp-lb

Description: Allow http from anywhere

Select VPC: noteapp-vpc

Inbound rule:

Add rule:

Typr: HTTP Source: Custom 0.0.0.0/0

Click "Create Security Group"

#### Third

Click "Create Security Group"

name: noteapp

Description: Allow http from loadbalancer

Select VPC: noteapp-vpc

Inbound rule:

Add rule:

Type: HTTP Source: Custom: Select the security Group: noteapp-lb

Click "Create Security Group"

### VPC Endpoints SSM

We need three Interface Endpoints for Session Manager

Go to VPC / Endpoints

Create Endpoint

Name: ssm

Type: AWS service

In services Search for: ssm

Select: com.amazonaws.eu-central-1.ssm

VPC: noteapp-vpc

Subnet: Select the two Availability Zones and select the private subnet in each

Security Group: noteapp

Policy: Full access

Create

Create two more for the folling Endpoints

- ssmmessages
- ec2messages

### VPC Endpoint DynamoDB

Create Endpoint

AWS service

Search: DynamoDB

Select Gateway

Select VPC

Select noteapp-rtb-private1
Select noteapp-rtb-private2

Full access

### EC2 Nat Instance

Create Instance

NatInstance
Amazon Linux
64bit x86
t2.micro 1 core 1 Gb Mem
New Keypair
    - name: natinstance
Network
    - noteapp-VPC
    - subnet noteapp-public-1
    - autoassign public IP
    - select existing Security Group noteapp-public-security-group

Advanced details:

- User data: Add script

```bash
#!/bin/bash
sudo yum update && sudo yum upgrade -y
sudo yum install iptables-services -y
sudo systemctl enable iptables
sudo systemctl start iptables
echo net.ipv4.ip_forward=1 | sudo tee -a /etc/sysctl.d/custom-ip-forwarding.conf
sudo sysctl -p /etc/sysctl.d/custom-ip-forwarding.conf
sudo /sbin/iptables -t nat -A POSTROUTING -o enX0 -j MASQUERADE
sudo /sbin/iptables -F FORWARD
sudo service iptables save

```

Actions / Networking / Change Source/Destination check

Stop

### VPC Route Tables

Update both

noteapp-rtb-private1-eu-central-1a
noteapp-rtb-private2-eu-central-1b

Edit Routes:

Add route:

0.0.0.0/0

Instance

natinstance

### EC2

Create Instance

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

Launch Instance

### Configure EC2

Connect using Session Manager

Test table access

```bash

aws dynamodb scan --table-name noteapp-table

```

Result:

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
