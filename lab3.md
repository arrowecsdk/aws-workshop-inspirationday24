# Lab 3

## VPC

Go to: __VPC__

Click: __Create VPC__

Use: __VPC and more__

Auto-generate: __noteapp__

IPv4 CIDR Block: __10.0.0.0/16__

Number of Availability Zones: __2__

Number of Public Subnet: __2__

Number of Private Subnet: __2__

No NAT Gateway

No VPC Endpoints

Click: __Create VPC__

Click: __View VPC__

You will get a great view in the __Resource Map__

## Security Groupz

Create three Security Groups

In the left menu click: __Security Groups__

### SG: noteapp-natinstance

Click: __Create Security Group__

Name: __noteapp-natinstance__

Description: __Allow localtraffic__

Select VPC: __noteapp-vpc__

Click __Add__ in Inbound rules

Type: __All Traffic__ Source: __Custom 10.0.0.0/16__

Click: __Create Security Group__

### SG: noteapp-lb

Click: __Create Security Group__

name: __noteapp-lb__

Description: __Allow http from anywhere__

Select VPC: __noteapp-vpc__

Click: __Add__ in Inbound rules

Type: __HTTP__ Source: __Custom 0.0.0.0/0__

Click: __Create Security Group__

### SG: noteapp

Click: __Create Security Group__

name: __noteapp__

Description: __Allow http from loadbalancer__

Select VPC: __noteapp-vpc__

Click: __Add__ in Inbound rules

Type: __HTTP__ Source: __Custom: Select the security Group: noteapp-lb__

Click: __Add__ in Inbound rules

Type: __HTTPS__ Source: __Custom: 0.0.0.0/0__

Click: __Add__ in Inbound rules

Type: __SSH__ Source: __Custom: 0.0.0.0/0__

Click: __Create Security Group__

## VPC Endpoints SSM

We need three Interface Endpoints for Session Manager

In the left menu go to: __Endpoints__

Click: __Create Endpoint__

Name: __ssm__

Type: __AWS service__

In services Search for: __ssm__

Select: __com.amazonaws.eu-north-1.ssm__

VPC: __noteapp-vpc__

Subnet: __Select the two Availability Zones and select the private subnet in each__

__Note:__

If you cant select the subnets, you need to refresh the page!

Security Group: __noteapp__

Policy: __Full access__

Click: __Create endpoint__

Create two more for the folling Endpoints

- ssmmessages
- ec2messages

## VPC Endpoint DynamoDB

Click: __Create Endpoint__

Name: __DynamoDB__

Type: __AWS service__

Search: __DynamoDB__

Select the __Gateway__ service

VPC: __noteapp-vpc__

Route Tables:

Select the two route tables:

- noteapp-rtb-private2-eu-north-1b
- noteapp-rtb-private1-eu-north-1a

Policy: __Full access__

Click: __Create endpoint__

[Go to Lab 4](lab4.md)
