# Lab 7

## Load Balancer - Target Group

Go to: __EC2__

In the left menu Select: __Target Groups__

Click: __Create Target Group__

Target Type: __Instances__

Target group Name: __noteapp-Target__

VPC: __noteapp-vpc__

Click: __Next__

Available Instances:

Select Nodes: __host1__ and __host2__

Click: __Include as pending below__

Click: __Create Target Group__

## Load Balancer - Application Load Balancer

Go to: __EC2__

In the left menu Select: __Load Balancer__

Click: __Create Load Balancer__

Select: __Application Load Balancer__

Click: __Create__

name: __noteapp-lb__

Schema: __Internet facing__

Network mapping:

VPC: __noteapp-vpc__

In Availability Zones Select:

- eu-north-1a (eun1-az1)
- eu-north-1b (eun1-az2)

Select both Public Subnets

Security Groups:

Delete: __default__

Select: __noteapp-lb__

listeners and Routing:

Select target group: __noteapp-target__

Leave the rest

Click: __Create Load Balancer__

In the Details:

Copy DNS name:

It looks like this __noteapp-lb-xxxxxxxxx.eu-north-1.elb.amazonaws.com__

Wait for the status: __Active__

## Test Website

Open a new tab in the browser - paste the url and test the web Application
