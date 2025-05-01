
# Clarusway Bootcamp Website Deployment – Solution

## Part 1: S3 Static Assets

- **Bucket Name**: `sda1025-clarusway-assets`
- **Region**: `eu-north-1`
- **Static Website URL**:  
  `http://sda1025-clarusway-assets.s3-website.eu-north-1.amazonaws.com`
- **Bucket Policy**:
```json
{
  "Version": "2012-10-17",
  "Statement": [{
    "Effect": "Allow",
    "Principal": "*",
    "Action": "s3:GetObject",
    "Resource": "arn:aws:s3:::sda1025-clarusway-assets/*"
  }]
}
```
- **Assets Uploaded**:
  - `index.html`
  - `logo.png`
  - `sda.png`

- **curl Test**:
```bash
curl -I http://sda1025-clarusway-assets.s3-website.eu-north-1.amazonaws.com
```

---

## Part 2: Auto Scaling Group

- **Launch Template Name**: `sda1025-launch-template`
- **User Data Script**:
```bash
#!/bin/bash
yum update -y
yum install -y nginx
systemctl start nginx
systemctl enable nginx
aws s3 cp s3://sda1025-clarusway-assets/index.html /usr/share/nginx/html/
aws s3 cp s3://sda1025-clarusway-assets/logo.png /usr/share/nginx/html/
aws s3 cp s3://sda1025-clarusway-assets/sda.png /usr/share/nginx/html/
```

- **ASG Name**: `sda1025-clarusway-ASG`
- **Configuration**:
  - Min: 1
  - Max: 3
  - Desired: 2
  - Health Checks: EC2 and ELB
- **Result**:
  - 2 EC2 instances launched
  - NGINX running and serving site

---

## Part 3: Application Load Balancer

- **ALB DNS**:  
  `http://sda1025-clarusway-alb-1643056834.eu-north-1.elb.amazonaws.com`
- **Listener**: HTTP (port 80)
- **Target Group**: Registered with 2 EC2 instances
- **Health Checks**: Passed for all targets

- **Round-Robin Test**:
```bash
for i in {1..5}; do curl -s http://sda1025-alb-123456.eu-north-1.elb.amazonaws.com | grep "hostname"; done
```
