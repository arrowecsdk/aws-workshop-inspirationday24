# Lab 5

## EC2 Host 1

Go to: EC2

Click: Launch Instance

Name: Host1

Application and OS Images:

OS: Amazon Linux

AMI: Amazon Linux 2023 AMI

Architecture: 64bit (Arm)

Instance Type: t4g.micro 2 core 1 Gb Mem

Keypair: Proceed without a key pair

Network Settings: Click Edit

VPC: Select noteapp-VPC

Subnet: Select noteapp-private-1

Autoassign public IP: Disable

Select existing Security Group: noteapp

Open Advanced details:

In IAM instance profile Select: noteapp-ec2

Click: Launch Instance

## Configure Host 1

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

[Go to Lab 6](lab6.md)
