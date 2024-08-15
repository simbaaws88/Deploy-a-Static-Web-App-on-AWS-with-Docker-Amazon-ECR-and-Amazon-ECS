![Architecture Diagram](22._Nat_Gateway_Reference_Architecture.jpg)
---

# Jupiter Project

This repository contains the infrastructure and deployment setup for the Jupiter project. The setup involves using Docker, AWS services (such as ECR, ECS, and Route 53), and GitHub for managing the source code and Docker images.

## Overview

The Jupiter project is a containerized application deployed on AWS using Elastic Container Service (ECS) and Fargate. This README will guide you through the steps required to set up the environment, build the Docker image, and deploy the application.

## Prerequisites

Before you start, ensure that you have the following installed and set up:

1. **Git** and **Visual Studio Code** on your computer.
2. A **GitHub** account and a **public SSH key** added to GitHub.
3. A **Docker Hub** account.
4. **Docker** installed on your computer.
5. **AWS CLI** installed and configured with an IAM user and named profile.
6. **AWS account** with access to services such as ECS, ECR, and Route 53.

## Architecture

The architecture involves the following components:

- **GitHub Repository**: Stores the source code and Dockerfile.
- **Docker Hub**: Used to push and pull Docker images.
- **Amazon ECR**: Stores Docker images in a private repository.
- **AWS ECS**: Manages containers using Fargate.
- **Application Load Balancer (ALB)**: Distributes incoming traffic across multiple containers.
- **Route 53**: DNS service used to manage the domain name for the application.
- **AWS Certificate Manager**: Secures web communications using SSL/TLS certificates.

## Steps to Set Up the Environment

### 1. Clone the GitHub Repository

Clone the GitHub repository to your local machine:

```bash
git clone git@github.com:your-username/jupiter.git
cd jupiter
```

### 2. Set Up Docker

- Sign up for a Docker Hub account.
- Install Docker on your computer if you haven't already.
- Create a `Dockerfile` using the script provided below.

### 3. Create the Dockerfile

Here’s the Dockerfile used for the Jupiter project:

```Dockerfile
FROM amazonlinux:latest

# Install dependencies
RUN yum update -y && \
    yum install -y httpd && \
    yum search wget && \
    yum install wget -y && \
    yum install unzip -y

# Change directory
RUN cd /var/www/html

# Download web files
RUN wget https://github.com/azeezsalu/techmax/archive/refs/heads/main.zip

# Unzip folder
RUN unzip main.zip

# Copy files into HTML directory
RUN cp -r techmax-main/* /var/www/html/

# Remove unwanted folder
RUN rm -rf techmax-main main.zip

# Expose port 80 on the container
EXPOSE 80

# Set the default application that will start when the container starts
ENTRYPOINT ["/usr/sbin/httpd", "-D", "FOREGROUND"]
```

### 4. Build and Run the Docker Image

Build the Docker image using the following command:

```bash
docker build -t jupiter .
```

To run the container locally:

```bash
docker run -p 80:80 jupiter
```

### 5. Push the Docker Image to Docker Hub

Create a repository in your Docker Hub account and push the image:

```bash
docker tag jupiter:latest your-dockerhub-username/jupiter:latest
docker push your-dockerhub-username/jupiter:latest
```

### 6. Set Up AWS Environment

- **Create an ECR Repository**: Use the AWS CLI to create a repository and push the image to ECR.
  
  ```bash
  aws ecr create-repository --repository-name jupiter --region us-east-1
  docker tag jupiter:latest 533267248939.dkr.ecr.us-east-1.amazonaws.com/jupiter:latest
  docker push 533267248939.dkr.ecr.us-east-1.amazonaws.com/jupiter:latest
  ```

- **Set Up VPC and Security Groups**: Create a VPC with public and private subnets, and configure security groups.

- **Create ECS Cluster and Task Definition**: Use ECS to create a cluster, define the task, and deploy the service.

- **Configure Application Load Balancer**: Set up an ALB to distribute traffic across your containers.

- **Register Domain with Route 53**: Create a hosted zone and set up the necessary DNS records.

- **Use AWS Certificate Manager**: Secure your domain with an SSL/TLS certificate.

## Final Steps

Once everything is set up, your application should be running and accessible via the domain name configured in Route 53.

## Troubleshooting

If you encounter any issues during the setup or deployment, check the logs in AWS CloudWatch and ensure that all services (ALB, ECS, etc.) are correctly configured.
