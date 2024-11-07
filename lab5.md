# Lab 5

## EC2 Host 1

Go to: __EC2__

Click: __Launch Instance__

Name: __Host1__

Application and OS Images:

OS: __Amazon Linux__

AMI: __Amazon Linux 2023 AMI__

Architecture: __64bit (Arm)__

Instance Type: __t4g.micro__ 2 core 1 Gb Mem

Keypair: __Proceed without a key pair__

Network Settings: __Click Edit__

VPC: __noteapp-VPC__

Subnet: __noteapp-private-1__

Autoassign public IP: __Disable__

Select existing Security Group: __noteapp__

Open Advanced details:

In IAM instance profile Select: __noteapp-ec2__

Click: __Launch Instance__

## Configure Host 1

Select the instance: __Host1__

At the top Click: __Connect__

Select: __Session Manager__

Click: __Connect__

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

Go through the next step line by line

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

sudo env PATH=$PATH:/usr/bin /usr/local/lib/node_modules/pm2/bin/pm2 startup systemd -u ec2-user --hp /home/ec2-user

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
