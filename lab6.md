# Lab 6

## EC2 Host2

Create the second host

Launch Instance

Go to: __EC2__

Click: __Launch Instance__

Name: __Host2__

Application and OS Images:

OS: __Amazon Linux__

AMI: __Amazon Linux 2023 AMI__

Architecture: __64bit (Arm)__

Instance Type: __t4g.micro__ 2 core 1 Gb Mem

Keypair: __Proceed without a key pair__

Network Settings: __Click Edit__

VPC: __noteapp-VPC__

Subnet: __noteapp-private-2__

Autoassign public IP: __Disable__

Select existing Security Group: __noteapp__

Open Advanced details:

In IAM instance profile Select: __noteapp-ec2__

User data: __add script__

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

Click: __Launch Instance__

[Go to Lab 7](lab7.md)
