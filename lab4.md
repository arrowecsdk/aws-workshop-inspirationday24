# Lab 4

## EC2 Nat Instance

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

## VPC Route Tables

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

[Go to Lab 5](lab5.md)
