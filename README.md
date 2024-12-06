MERN Stack Application Deployment
Overview
This documentation provides a step-by-step guide to deploying a MERN stack application on AWS using EC2 instances, an Application Load Balancer (ALB), and Cloudflare for DNS and domain management.

Key Details
Load Balancer DNS: ShivamTMALB-192579021.us-east-1.elb.amazonaws.com
Domain: theshiv.xyz


Deployment Architecture
Below is the deployment architecture for this setup:


The architecture includes:

A front-end and back-end MERN stack application hosted on multiple EC2 instances (using AMIs for replication).
An Application Load Balancer (ALB) for distributing traffic across instances.
Cloudflare for DNS and domain management, pointing to the load balancer endpoint.
Custom domain registration via GoDaddy.
Steps to Deploy the Application
1. Initial EC2 Setup
Launch an EC2 Instance:

Use Amazon Linux 2 or Ubuntu as the AMI.
Assign a security group with HTTP (port 80) and HTTPS (port 443) access.
SSH into the instance.
Install Dependencies:

Install Node.js, NPM
Use the following commands:
bash
sudo yum update -y
sudo yum install -y nodejs npm git


Clone your MERN application repository:
bash
git clone https://github.com/UnpredictablePrashant/TravelMemory
cd TravelMemory
Configure the Backend:


Navigate to the backend folder:
bash
cd backend
Update the .env file with your database connection string and application settings.
Start the Application:'


Start the backend server:
bash
npm install
npm start
Ensure the backend is running on port 3000.
Set Up the Frontend:

Navigate to the frontend folder:
bash
cd ../frontend
Build the production frontend files to create and update the .env file
bash
npm install
npm run build
Configure Nginx:

Set up a reverse proxy using Nginx to route traffic:
bash
sudo yum install -y nginx
sudo nano /etc/nginx/nginx.conf
Example configuration:
nginx
Copy code
server {
    listen 80;

    location /api/ {
        proxy_pass http://localhost:3000/;
    }

    location / {
        root /path/to/frontend/build;
        index index.html;
    }
}
Restart Nginx:
bash
sudo systemctl restart nginx


2. Create an AMI and Launch Additional Instances
Create an AMI:

Once the application is running, create an Amazon Machine Image (AMI) of the configured EC2 instance.
Launch New Instances:

Launch two additional instances using the created AMI.
Assign the same security group to all instances.
Verify:

SSH into the instances to verify the application is running.
3. Configure Load Balancing
Create a Target Group:

Name: shivam1TMTG.
Target type: Instances.
Protocol: HTTP.
Health Check Path: /.
Create an Application Load Balancer (ALB):

Name: shivamTMALB.
Listener: HTTP on port 80.
Add shivam1TMTG as the target group.
Test the Load Balancer:

Access the ALB DNS endpoint (ShivamTMALB-192579021.us-east-1.elb.amazonaws.com) in a browser to confirm it routes traffic to your instances.
4. Configure Domain with Cloudflare
Add Your Domain to Cloudflare:

Log in to your Cloudflare account and add theshiv.xyz.
Copy the nameservers provided by Cloudflare.
Update Nameservers in GoDaddy:

Log in to your GoDaddy account and update the domain’s nameservers with those from Cloudflare.
Add DNS Records in Cloudflare:

Create a CNAME record:
Name: www
Target: ShivamTMALB-192579021.us-east-1.elb.amazonaws.com.
Create an A record:
Name: @
Value: The public IP of one of the instances (optional if the ALB handles all traffic).
Verify Domain:

Visit theshiv.xyz in a browser to ensure your application is accessible.
Final Outcome
The MERN stack application is now successfully deployed and accessible via your custom domain: theshiv.xyz.
