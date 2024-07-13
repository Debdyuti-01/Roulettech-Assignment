# MedTrack Pal : Medication Reminder Application

## Overview

'MedTrack Pal' is a Medication Reminder application with a React.js frontend and a Django backend. The project aims to help users manage their medication schedules effectively. This guide will talk about the tech stack, architecture, and deployment process to AWS.

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
  
## Features of MedTrack Pal
- User Authentication and Authorization
- Profile Management
- Medicine Scheduling
- Reminder Notifications
- Medication History
- Doctor and Pharmacy Information
- Customizable Reminders
- User Dashboard
- Secure Data Handling

## API Endpoints
- **/api/register** : `POST` -  Register a new user
```
curl -X POST http://127.0.0.1:8000/api/register/ -H "Content-Type: application/json" -d '{"username":"testuser", "password":"testpassword", "password2":"testpassword", "email":"test@example.com"}'
```  
- **/api/token/** : `GET` - Obtain a token
```
curl -X POST http://127.0.0.1:8000/api/token/ -H "Content-Type: application/json" -d '{"email":"test@example.com", "username":"testuser", "password":"testpassword"}'
```
- **api/** : `GET` - Get routes
```
curl http://127.0.0.1:8000/api/ -H "Authorization: Bearer YOUR_ACCESS_TOKEN"
```
- **api/notes/** : `GET` - Returns an array of notes
```
curl http://127.0.0.1:8000/api/notes/ -H "Authorization: Bearer YOUR_ACCESS_TOKEN"
```
- **api/notes/<id>** : `GET` - Returns a single note object
```
curl http://127.0.0.1:8000/api/notes/<id>/ -H "Authorization: Bearer YOUR_ACCESS_TOKEN"
```
- **api/notes/create/** : `POST` - Creates new note with data sent in post request
```
curl -X POST http://127.0.0.1:8000/api/notes/create -H "Authorization: Bearer YOUR_ACCESS_TOKEN" -H "Content-Type: application/json" -d '{"body": {"medicine_name": "Aspirin", "route_of_administration": "Oral", "dosage_form": "Tablet", "dosage_unit_of_measure": "mg", "dosage_quantity_of_units_per_time": 500, "regimen_note": "Take with food", "periodic_interval": 24, "dosage_frequency": 1, "first_time_of_intake": "2023-07-13T08:00:00Z", "is_chronic_or_acute": "Acute", "stopped_by_datetime": "2023-07-20T08:00:00Z"}}'
```
- **api/notes/id>/update/** : `PUT` - Creates an existing note with data sent in post request
```
curl -X PUT http://127.0.0.1:8000/api/notes/<id>/update -H "Authorization: Bearer YOUR_ACCESS_TOKEN" -H "Content-Type: application/json" -d '{"body": {"medicine_name": "Updated Aspirin", "route_of_administration": "Oral", "dosage_form": "Tablet", "dosage_unit_of_measure": "mg", "dosage_quantity_of_units_per_time": 1000, "regimen_note": "Take with water", "periodic_interval": 12, "dosage_frequency": 2, "first_time_of_intake": "2023-07-13T08:00:00Z", "is_chronic_or_acute": "Chronic", "stopped_by_datetime": null}}'
```
- **api/notes/<id>/delete/** : `DELETE` - Deletes and exiting note
```
curl -X DELETE http://127.0.0.1:8000/api/notes/<id>/delete -H "Authorization: Bearer YOUR_ACCESS_TOKEN"
```
## Run the application locally

### 1. Run the Frontend
```
cd react-js
```
```
npm install
```
```
npm start
```
### 2. Run the Backend 
```
python3 manage.py runserver
```
## Deployment to AWS

### 1. Deploy the Frontend (React.js) to AWS S3

#### a. Create an S3 Bucket

1. Go to the [AWS S3 console](https://s3.console.aws.amazon.com/s3/home).
2. Click "Create bucket".
3. Choose a unique name(I've given bucket_name as: medtrack-pal) and select a region.
4. Enable "Static website hosting" in the bucket properties.

#### b. Build the React App

1. In the React project directory, run:
```
npm run build
```
2. The above command will create a `build` folder with optimized production files.

#### c. Upload to S3

1. Upload the contents of the `build` folder to your S3 bucket.
2. Make sure `index.html` is at the root of the bucket.
3. Set appropriate permissions for public access.
4. Update the S3 Bucket Policy
```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublicReadGetObject",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::medtrack-pal/*"
        }
    ]
}
```

#### d. Configure Bucket for Static Website Hosting

1. In bucket properties, set the index document to `index.html`.
2. Note the bucket's website endpoint (For MedTrack Pal: `http://medtrack-pal.s3-website.us-east-2.amazonaws.com/login`)

### 2. Deploy the Backend (Django) to AWS EC2

#### a. Launch an EC2 Instance

1. Go to the [EC2 dashboard](https://console.aws.amazon.com/ec2/home) and click "Launch Instance".
2. Choose an Amazon Linux 2 AMI.
3. Select an instance type (t2.micro is fine for testing).
4. Configure instance details, add storage, and configure security group (allow HTTP, HTTPS, SSH and Custom TCP with Port 8000 ).
5. Launch the instance and create/select a key pair.

#### b. Connect to Your EC2 Instance

1. Used SSH to connect:
```
sudo ssh -i ~/django.pem ec2-user@ec2-3-17-66-119.us-east-2.compute.amazonaws.com 
```
#### c. Set Up the Environment

1. Update the system:
```
sudo yum update -y
```
2. Install Python:
```
sudo yum install python3 -y
```
3. Install pip:
```
sudo yum install python3-pip3 -y
```
4. Install virtual environment:
```
sudo pip3 install virtualenv
```

#### d. Deploy the Django App

1. Transfer your Django project to the EC2 instance (use SCP or git).
2. Create and activate a virtual environment:
3. Install dependencies:
```
 pip3 install -r requirements.txt
```
4. Collect static files:
```
python3 manage.py collectstatic
```
5. Make migrations:
```
 python3 manage.py makemigrations
```
6. Run migrations:
```
python manage.py migrate
```
### 3. Ensure Public Accessibility

1. Update the Django settings to allow your EC2 public DNS in `ALLOWED_HOSTS` in settings.py file
```
ALLOWED_HOSTS = ['3.17.66.119', 'localhost', '127.0.0.1']
```
2. Configured my React app to use the EC2 public DNS for API calls.

### 4. Use AWS CloudFront for CDN (Optional)

1. Go to the [CloudFront console](https://console.aws.amazon.com/cloudfront/home) and create a new distribution.
2. Use your S3 bucket website endpoint as the origin.
3. Update S3 Bucket Policy
```
   {
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublicReadGetObject",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::medtrack-pal/*"
        }
    ]
}
```
5. Configure caching behavior and other settings.
6. Note the CloudFront distribution domain name (For me it is: `https://d3jxrb3r2dl9tk.cloudfront.net`)

### 5. Create a Custom VPC with Private Subnet (Optional)

1. Go to the [VPC dashboard](https://console.aws.amazon.com/vpc/home) and create a new VPC.
2. Create a private subnet within this VPC.
3. Set up a NAT gateway for outbound internet access.
4. Launch your EC2 instance in this private subnet.
5. Use a bastion host or AWS Systems Manager for SSH access.

## Conclusion

Following these steps that helped me to successfully deploy my MedTrack Pal:Medicine Reminder application with a React.js frontend and Django backend to AWS. This setup ensures scalability, security, and high availability. 
