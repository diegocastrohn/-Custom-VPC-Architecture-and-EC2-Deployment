Apache Deployment with EC2 User Data

This project demonstrates how to deploy a public Apache web server on Amazon EC2 and automatically configure the instance using EC2 User Data.

The laboratory also includes a real troubleshooting scenario in which the first automated deployment failed because the instance could not access package repositories during its initial boot.

A second EC2 instance was then deployed successfully after correcting the infrastructure.

🎯 Objectives
Deploy an Amazon EC2 web server.
Configure HTTP and SSH access.
Install Apache HTTP Server.
Host a custom HTML page.
Automate server configuration with EC2 User Data.
Understand cloud-init.
Troubleshoot networking and application failures.
Validate automation through redeployment.
🏗️ Architecture




Internet
    │
    │ HTTP :80
    ▼
Internet Gateway
    │
    ▼
Public Route Table
0.0.0.0/0 → IGW
    │
    ▼
Public Subnet
10.2.1.0/24
    │
    ▼
Amazon EC2
Amazon Linux 2023
    │
    ▼
Apache HTTP Server
    │
    ▼
Custom Website
🌐 Network Configuration
Component	Configuration
VPC	10.2.0.0/16
Public Subnet	10.2.1.0/24
Internet Route	0.0.0.0/0 → IGW
HTTP	TCP/80
SSH	TCP/22
🛡️ Security

The EC2 Security Group follows a simple least-privilege model.

SSH

TCP/22 ← Administrator Public IP /32

HTTP

TCP/80 ← 0.0.0.0/0

SSH administration is restricted while HTTP is publicly accessible.

🤖 EC2 User Data

The final deployment uses User Data to automatically install and configure Apache.

#!/bin/bash

dnf install -y httpd

systemctl enable --now httpd

cat > /var/www/html/index.html <<'EOF'
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>AWS Lab 04</title>
</head>
<body>

<h1>AWS Cloud Lab 04</h1>

<h2>Automated Deployment Successful!</h2>

<p>
This Apache web server was automatically deployed
using Amazon EC2 User Data.
</p>

<h3>Architecture</h3>

<p>
Internet → Internet Gateway → Public Subnet → EC2 → Apache
</p>

<hr>

<p>AWS Cloud Portfolio — Lab 04</p>

</body>
</html>
EOF
🔄 Automated Deployment Flow
Launch EC2
    │
    ▼
cloud-init
    │
    ▼
EC2 User Data
    │
    ├── Install Apache
    ├── Enable Apache
    ├── Start Apache
    └── Create index.html
    │
    ▼
HTTP :80
    │
    ▼
Website Available
🔧 Troubleshooting

The first deployment did not succeed.

Initially:

Test	Result
SSH TCP/22	❌ FAIL
HTTP TCP/80	❌ FAIL

The network configuration was reviewed and corrected.

Afterward:

Test	Result
SSH TCP/22	✅ PASS
HTTP TCP/80	❌ FAIL

Because SSH worked, the investigation moved from networking to the application.

Apache Investigation

Apache status:

sudo systemctl status httpd

Result:

Unit httpd.service could not be found

Local HTTP:

curl http://localhost

Result:

Failed to connect

Port inspection:

sudo ss -tulpn | grep :80

No service was listening on TCP/80.

Conclusion

Apache had not been installed.

Cloud-Init Investigation

Cloud-init reported:

status: done

The User Data script was present on the instance.

However, log inspection revealed an error related to downloading package repository metadata:

Failed to download metadata
Root Cause

The EC2 instance did not have working Internet connectivity when User Data attempted to install Apache during the initial boot.

Therefore:

EC2 Boot
   ↓
User Data
   ↓
dnf install httpd
   ↓
Repository access
   ↓
Internet unavailable
   ↓
Installation failed
Manual Validation

After restoring connectivity, Apache was manually installed on the first EC2.

sudo dnf install -y httpd
sudo systemctl enable --now httpd

The Apache default page became publicly accessible.

This proved that the network architecture and HTTP configuration were functioning correctly.

🚀 Automated Deployment Validation

A second EC2 instance was launched after the networking configuration was corrected.

The simplified User Data script was supplied during instance launch.

No manual installation was performed.

The result:

New EC2
   ↓
Internet available
   ↓
User Data
   ↓
Apache installed
   ↓
Apache started
   ↓
HTML created
   ↓
Website available

Result: ✅ PASS

🧪 Validation Matrix
Test	Result
Public EC2 deployment	✅ PASS
Internet Gateway connectivity	✅ PASS
SSH access	✅ PASS
HTTP TCP/80	✅ PASS
Apache installation	✅ PASS
Apache service	✅ PASS
Custom HTML deployment	✅ PASS
User Data automation	✅ PASS
Website accessible from Internet	✅ PASS
📸 Implementation Evidence

Suggested repository screenshots:

screenshots/
├── 01-vpc.png
├── 02-public-subnet.png
├── 03-route-table.png
├── 04-security-group.png
├── 05-first-ec2-failure.png
├── 06-cloud-init-error.png
├── 07-automated-ec2.png
├── 08-user-data.png
├── 09-http-test.png
└── 10-automated-website.png

Sensitive public IP addresses, credentials, account information, and private keys should not be included.

🛠️ Technologies & Skills
AWS
Amazon VPC
Amazon EC2
Internet Gateway
Route Tables
Security Groups
EC2 User Data
Linux
Amazon Linux 2023
Apache HTTP Server
dnf
systemctl
curl
ss
cloud-init
Networking
HTTP
TCP/80
SSH
TCP/22
Public IPv4
Internet routing
Troubleshooting
Layer-by-layer network troubleshooting
Service validation
Port testing
Cloud-init log analysis
Root cause analysis
🧠 Key Lessons

The most important lesson from this project was that infrastructure automation depends on the infrastructure itself being ready.

User Data can contain a valid script while still failing because an external dependency, such as Internet connectivity, is unavailable.

The project also demonstrated why troubleshooting should move systematically through layers:

Network
   ↓
Transport
   ↓
Operating System
   ↓
Service
   ↓
Automation
   ↓
Logs

The second deployment validated that the corrected architecture and automation were repeatable.

🚀 Future Improvements

Possible extensions include:

HTTPS/TLS
Application Load Balancer
Multiple EC2 web servers
Health checks
Auto Scaling
CloudWatch monitoring
Private application servers
Infrastructure as Code
Project Status

Successfully Completed ✅

Focus: AWS • EC2 • Linux • Apache • Networking • User Data • Automation • Troubleshooting
