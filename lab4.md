# Lab 4

## EC2 Nat Instance

Create a Nat Instance

Go to: __EC2__

Click: __Launch Instance__

Name: __NatInstance__

Application and OS Images:

OS: __Amazon Linux__

AMI: __Amazon Linux 2023 AMI__

Architecture: __64bit (x86)__

Instance Type: __t3.micro__ 1 core 1 Gb Mem

Keypair: __Proceed without a key pair__

Network Settings: __Click Edit__

VPC: __noteapp-VPC__

Subnet: __noteapp-public-1__

Autoassign public IP: __Enable__

Select existing Security Group: __noteapp-natinstance__

Open Advanced details:

In User data: __Add script__

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

When the instance is running:

Select the instance: __NatInstance__

Click: __Actions / Networking / Change Source/Destination check__

Select: __Stop__

Click: __Save__

## VPC Route Tables

Go to: __VPC__

In the left menu Click: __Route Tables__

For both private Route Tables:

- noteapp-rtb-private2-eu-north-1b
- noteapp-rtb-private1-eu-north-1a

Click: __Edit Routes__

Click: __Add route__

Destination: __0.0.0.0/0__

Target: __Select Instance__

Select: __Natinstance__

Click: __Save Changes__

[Go to Lab 5](lab5.md)
