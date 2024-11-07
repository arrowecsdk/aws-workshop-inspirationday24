# Lab 7

## Load Balancer - Target Group

Go to: EC2

In the left menu Select: Target Groups

Click: Create Target Group

Target Type: Instances

Name: noteapp-Target

VPC: noteapp-vpc

Select Nodes: host1 and host2

Click: Include as pending below

## Load Balancer - Application Load Balancer

Go to: EC2

In the left menu Select: Load Balancer

Click: Create Load Balancer

Select: Application Load Balancer

name: noteapp-lb

Schema: Internet facing

Network mapping:

VPC: noteapp-vpc

In Availability Zones Select:

- eu-north-1a (eun1-az1)
- eu-north-1b (eun1-az2)

Select both Public Subnets

Security Groups:

Delete: default

Select: noteapp-lb

listeners and Routing:

Select target group: noteapp-target

Leave the rest

Click: Create Load Balancer

In the Details:

Copy DNS name:

## Test Website

Open a new tab in the browser - paste the url and test the web Application
