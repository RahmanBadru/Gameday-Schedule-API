# Sports API Management System

## Overview

This project demonstrates building a containerized api management system for querying sports data. It uses *Amazon ECS* for running containers, *Amazon API Gateway* for exposing REST Endpoints and an external Sports API for real-time sports data. The project showcases advanced cloud computing practices, including API management, container orchestration, and secure AWS integrations.

## Features

- REST API for querying sports data
- Containerized backend with Amazon ECS (Fargate)
- API Management and Routing with API Gateway

## Prerequisites
- Sports API Key: Sign up for a free account and subscription & obtain your API Key at serpapi.com
- AWS CLI Installed and Configured: Install & configure AWS CLI to programatically interact with AWS
- AWS Account: Create an AWS Account & have basic understanding of ECS, API Gateway, Docker & Python
- Docker CLI and Desktop Installed: To build & push container images

## Technologies:

- Cloud Provider: AWS
- Core Services: Amazon ECS (Fargate), API Gateway, CloudWatch
- Programming Language: Python 3.x
- Containerization: Docker
- IAM Security: Custom least privilege policies for ECS task execution and API Gateway

### Project Structure

```sh
sports-api-management/
├── app.py             # Flask application for querying sports data
├── Dockerfile         # Dockerfile to containerize the Flask app
├── requirements.txt   # Python dependencies
├── .gitignore         
└── README.md          # Project documentation
```

Setup Instructions

- Clone the Repository
```sh
git clone https://github.com/ifeanyiro9/containerized-sports-api.git
cd containerized-sports-api
```

-Create an ECR Repository
`aws ecr create-repository --repository-name sports-api --region us-east-1`

- Authenticate, Build, and Push Docker Image

```sh
aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin <AWS_ACCOUNT_ID>.dkr.ecr.us-east-1.amazonaws.com

docker build --platform linux/amd64 -t sports-api .
docker tag sports-api:latest <AWS_ACCOUNT_ID>.dkr.ecr.us-east-1.amazonaws.com/sports-api:sports-api-latest
docker push <AWS_ACCOUNT_ID>.dkr.ecr.us-east-1.amazonaws.com/sports-api:sports-api-latest
```

- Set Up an ECS Cluster with Fargate
  - Create an ECS Cluster:
    - Navigate to the ECS Console → Clusters → Create Cluster.
    - Name the cluster (e.g., sports-api-cluster).
    - Choose Fargate as the infrastructure type, then create the cluster.

- Create a Task Definition:

- Go to Task Definitions → Create New Task Definition.
  - Name the task definition (e.g., sports-api-task).
  - Choose Fargate as the infrastructure type.
  - Add the container:
    - Name: sports-api-container
    - Image URI: <AWS_ACCOUNT_ID>.dkr.ecr.us-east-1.amazonaws.com/sports-api:sports-api-latest
    - Container Port: 8080
    - Environment Variables:
      - Key: SPORTS_API_KEY
      - Value: <YOUR_SPORTSDATA.IO_API_KEY>
    - Save and create the task definition.

- Deploy the Service with an Application Load Balancer (ALB):
  - Go to Clusters → Select Cluster → Service → Create Service.
  - Configuration:
    - Capacity Provider: Fargate
    - Task Definition: sports-api-task
    - Service Name: sports-api-service
    - Desired Tasks: 2
    - Networking:
      - Create a new security group allowing TCP traffic.
      - Type: All TCP, Source: Anywhere
    - Load Balancer:
      - Select Application Load Balancer (ALB).
      - ALB Configuration:
        - Create a new ALB named sports-api-alb.
        - Health Check Path: /sports
    - Create the service.
- Test the ALB:
  - Retrieve the ALB DNS name (e.g., sports-api-alb-<AWS_ACCOUNT_ID>.us-east-1.elb.amazonaws.com).
  - Verify the API is accessible by visiting http://sports-api-alb-<AWS_ACCOUNT_ID>.us-east-1.amazonaws.com/sports.

- Configure API Gateway
  - Create a REST API:
    - Navigate to API Gateway Console → Create API → REST API.
    - Name the API (e.g., Sports API Gateway).
  - Set Up Integration:
    - Create a resource /sports.
    - Create a GET method and choose HTTP Proxy as the integration type.
    - Enter the ALB DNS name followed by /sports.
  - Deploy the API:
  -Deploy the API to a stage (e.g., prod) and note the endpoint URL.

- Test the System
Use a browser or curl to test:
`curl https://<api-gateway-id>.execute-api.us-east-1.amazonaws.com/prod/sports`


### Key Learnings:

- Building a scalable, containerized application using ECS and Fargate.
- Creating a public API with API Gateway.

### Future Enhancements:

- Implement caching for frequent API requests using Amazon ElastiCache.
- Add DynamoDB to store user-specific queries and preferences.
- Enhance API security with API keys or IAM-based authentication.
- Set up CI/CD pipelines for automated container deployments.






