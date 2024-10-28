# AWS Workshop Inspiration Day 2024

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

noteapp-table-Policy

```

IAM / Roles

Create Role

EC2

Permissions: noteapp-table-Policy

Rolename:
noteapp-table-access



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

### EC2

Create Instance

Host1
Amazon Linux
64bit ARM
t4g.micro 2 core 1 Gb Mem
New keypair
    - noteapp
Network
    - noteapp-VPC
    - subnet noteapp-public-1
    - autoassign public IP
    - select existing Security Group noteapp-public

Advanced details
    - IAM instance profile

Launch Instance

ssh -i "Downloads/noteapp.pem" <ec2-user@ec2-3-76-106-241.eu-central-1.compute.amazonaws.com>

### Configure EC2

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
# 1. Update the system
sudo dnf update -y

# 2. Install Node.js (using Node.js 20, the current LTS)
sudo dnf install nodejs -y

# 3. Verify installation
node --version
npm --version

# 4. Create directory and initialize project
mkdir note-app
cd note-app
npm init -y

# 5. Install dependencies
npm install express aws-sdk body-parser

# 6. Create directory structure and files
mkdir public

# 7. Install PM2 globally
sudo npm install -g pm2

# 8. Start the application with PM2
pm2 start server.js

# 9. Make PM2 start on system boot
pm2 startup
# Run the command that PM2 gives you

# 10. Save the PM2 process list
pm2 save

```

Install Nginx

```bash
# Install nginx
sudo dnf install nginx -y

# Start nginx
sudo systemctl start nginx
sudo systemctl enable nginx

# Create nginx configuration
sudo nano /etc/nginx/conf.d/note-app.conf

```

````bash
Copyserver {
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

```

Test httpd

Get the instance ip

Instance summery

Open a browser and test

"It Works!"

Website

```bash

curl 'https://raw.githubusercontent.com/arrowecsdk/aws-workshop-inspirationday24/refs/heads/main/noteapp/index.html' > index.html

cp index.html /var/www/html/index.html



```

DynamoDB

-table

Gateway-Endpoint for DynamoDB
