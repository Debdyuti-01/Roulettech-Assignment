# MedTrack Pal : Medication Reminder Application

## Overview

This repository contains a Medicine Reminder application with a React.js frontend and a Django backend. The project aims to help users manage their medication schedules effectively. This guide will walk you through the tech stack, architecture, and deployment process to AWS.

## Tech Stack

- **Frontend**: React.js
- **Backend**: Django
- **Database**: SQLite (for development)
- **Deployment**: AWS S3 (frontend), AWS EC2 (backend), AWS CloudFront (optional CDN), Custom VPC (optional)

## Architecture

- **Frontend**: Built with React.js and deployed on AWS S3 as a static website.
- **Backend**: Developed using Django, deployed on an AWS EC2 instance.
- **Communication**: The frontend interacts with the backend via RESTful APIs.
- **Static Files**: Served by S3 and collected by Django.
- **Optional**: CloudFront is used as a CDN for the frontend, and a custom VPC can be created for enhanced security.

## Deployment to AWS

### 1. Deploy the Frontend (React.js) to AWS S3

#### a. Create an S3 Bucket

1. Go to the [AWS S3 console](https://s3.console.aws.amazon.com/s3/home).
2. Click "Create bucket".
3. Choose a unique name and select a region.
4. Enable "Static website hosting" in the bucket properties.

#### b. Build Your React App

1. In your React project directory, run:
2. This creates a `build` folder with optimized production files.

#### c. Upload to S3

1. Upload the contents of the `build` folder to your S3 bucket.
2. Set appropriate permissions for public access.

#### d. Configure Bucket for Static Website Hosting

1. In bucket properties, set the index document to `index.html`.
2. Note the bucket's website endpoint.

### 2. Deploy the Backend (Django) to AWS EC2

#### a. Launch an EC2 Instance

1. Go to the [EC2 dashboard](https://console.aws.amazon.com/ec2/home) and click "Launch Instance".
2. Choose an Amazon Linux 2 AMI.
3. Select an instance type (t2.micro is fine for testing).
4. Configure instance details, add storage, and configure security group (allow HTTP, HTTPS, and SSH).
5. Launch the instance and create/select a key pair.

#### b. Connect to Your EC2 Instance

1. Use SSH to connect:

#### c. Set Up the Environment

1. Update the system:
2. Install Python:
3. Install pip:
4. Install virtual environment:

#### d. Deploy Your Django App

1. Transfer your Django project to the EC2 instance (use SCP or git).
2. Create and activate a virtual environment:
3. Install dependencies:
4. Collect static files:
5. Run migrations:

#### e. Set Up Gunicorn and Nginx

1. Install Gunicorn:
3. Configure Nginx to proxy requests to Gunicorn.
4. Start Gunicorn and Nginx:

### 3. Ensure Public Accessibility

1. Update your Django settings to allow your EC2 public DNS in `ALLOWED_HOSTS`.
2. Configure your React app to use the EC2 public DNS for API calls.

### 4. Use AWS CloudFront for CDN (Optional)

1. Go to the [CloudFront console](https://console.aws.amazon.com/cloudfront/home) and create a new distribution.
2. Use your S3 bucket website endpoint as the origin.
3. Configure caching behavior and other settings.
4. Note the CloudFront distribution domain name.

### 5. Create a Custom VPC with Private Subnet (Optional)

1. Go to the [VPC dashboard](https://console.aws.amazon.com/vpc/home) and create a new VPC.
2. Create a private subnet within this VPC.
3. Set up a NAT gateway for outbound internet access.
4. Launch your EC2 instance in this private subnet.
5. Use a bastion host or AWS Systems Manager for SSH access.

## Conclusion

Following these steps will help you successfully deploy your Medicine Reminder application with a React.js frontend and Django backend to AWS. This setup ensures scalability, security, and high availability. If you encounter any issues or need further assistance, feel free to open an issue on this repository.
