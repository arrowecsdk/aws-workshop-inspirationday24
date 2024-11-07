# Lab 6

## EC2 Host2

Create the second host

Launch Instance

Go to: EC2

Click: Launch Instance

Name: Host2

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

User data: add script

```bash
#!/bin/bash
dnf update -y
dnf install nodejs -y
cd /home/ec2-user
mkdir -p note-app/public
chown -R ec2-user:ec2-user note-app
cd note-app
sudo -u ec2-user npm init -y
sudo -u ec2-user npm install express aws-sdk body-parser
npm install -g pm2
sudo -u ec2-user curl 'https://raw.githubusercontent.com/arrowecsdk/aws-workshop-inspirationday24/refs/heads/main/noteapp/server.js' > /home/ec2-user/note-app/server.js
sudo -u ec2-user curl 'https://raw.githubusercontent.com/arrowecsdk/aws-workshop-inspirationday24/refs/heads/main/noteapp/public/index.html' > /home/ec2-user/note-app/public/index.html
chown -R ec2-user:ec2-user /home/ec2-user/note-app
cd /home/ec2-user/note-app
sudo -u ec2-user PM2_HOME=/home/ec2-user/.pm2 pm2 start server.js
sudo -u ec2-user PM2_HOME=/home/ec2-user/.pm2 pm2 startup systemd -u ec2-user --hp /home/ec2-user
sudo -u ec2-user PM2_HOME=/home/ec2-user/.pm2 pm2 save
dnf install nginx -y
cat > /etc/nginx/conf.d/note-app.conf << 'EOF'
server {
    listen 80;
    server_name _;

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
rm -f /etc/nginx/conf.d/default.conf
systemctl start nginx
systemctl enable nginx

```

Click: Launch Instance

[Go to Lab 7](lab7.md)
