# Lab 3

## VPC

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

## Security Groupz

Create three Security Groups

In the left menu click: Security Groups

### SG: noteapp-natinstance

Click "Create Security Group"

Name: noteapp-natinstance

Description: Allow localtraffic

Select VPC: noteapp-vpc

Click Add in Inbound rules

Type: All Traffic Source: Custom 10.0.0.0/16

Click "Create Security Group"

### SG: noteapp-lb

Click "Create Security Group"

name: noteapp-lb

Description: Allow http from anywhere

Select VPC: noteapp-vpc

Click Add in Inbound rules

Type: HTTP Source: Custom 0.0.0.0/0

Click "Create Security Group"

### SG: noteapp

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

## VPC Endpoints SSM

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

## VPC Endpoint DynamoDB

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

[Go to Lab 4](lab4.md)
