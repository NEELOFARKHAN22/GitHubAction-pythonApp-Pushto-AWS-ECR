# GitHubAction-pythonApp-Pushto-AWS-ECR
# CI/CD Pipeline using GitHub Actions for AWS ECS

This repository contains the proof of concept (POC) for setting up a CI/CD pipeline using GitHub Actions to deploy a Dockerized Python application to AWS ECS.

## Overview

In this project, I explore the basic concepts of GitHub Actions pipeline. The goal of the POC is to push a Docker Python application image to AWS ECR (Elastic Container Registry) using GitHub Actions and deploy it to AWS ECS (Elastic Container Service).

## Steps

### 1. Setting Up the Repository

- Create a GitHub repository with the necessary files:
  - Dockerfile
  - Python.py
  - requirement.txt
  - Index.html
- Set up a GitHub Actions workflow (`main.yml`) to define the pipeline stages.
  - (.github/workflows/main.yml).

### 2. Configuring AWS ECR

- Create a private repository in AWS ECR.
- Copy the repository URL for later use.

### 3. Managing Secrets

- Add secret variables to the GitHub repository:
  - Repository URL from AWS ECR
  - Access Key, Secret Key, and Region Name
- Create an IAM user with permissions related to ECR and generate access and secret keys.

### 4. Implementing the CI/CD Pipeline

- Commit changes to trigger the GitHub Actions workflow.
- GitHub Actions builds the Docker image and pushes it to AWS ECR.
- Retrieve the image URL from AWS ECR.

### 5. Deploying to AWS ECS

- Create a Task Definition (TD) referencing the Docker image from ECR.
- Create an ECS cluster and service:
  - The service will use the Task Definition and launch containers.
  - Implement ALB (Application Load Balancer) for traffic routing.
  - Utilize rolling update deployment for seamless updates.

## Conclusion

This README provides a step-by-step guide to set up a CI/CD pipeline using GitHub Actions for deploying Dockerized applications to AWS ECS. Follow these instructions to streamline your development and deployment processes.

Feel free to contribute or provide feedback!

## Additional Information on main.yml Keywords

- `uses`: Specifies the action to be used for checking out the repository, configuring AWS credentials, and logging in to Amazon ECR.
- `name`: Provides a descriptive name for each step in the workflow.
- `runs-on`: Defines the operating system environment on which the job will run.
- `steps`: Contains a list of actions or commands to be executed as part of the job.
- `with`: Specifies input parameters or configuration options for an action.
- `env`: Sets environment variables to be used during the job execution.
- `run`: Executes shell commands or scripts within the job environment.
