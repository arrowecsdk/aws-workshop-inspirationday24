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

Install Webserver

```bash

sudo su -

yum update

yum install httpd -y

systemctl enable httpd

systemctl start httpd

```

Test httpd

Get the instance ip

Instance summery

Open a browser and test

"It Works!"

Website

```bash

curl 'https://raw.githubusercontent.com/arrowecsdk/aws-workshop-inspirationday24/refs/heads/main/noteapp/index.html' > index.html

cp index.html 

```

DynamoDB

-table

Gateway-Endpoint for DynamoDB
