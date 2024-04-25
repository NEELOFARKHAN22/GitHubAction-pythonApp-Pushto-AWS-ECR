# CI/CD Pipeline using GitHub Actions for AWS ECS 

Setting up a CI/CD pipeline using GitHub Actions to deploy a Dockerized Python application to AWS ECS.

## Table of Contents

1. [Overview](#overview)
2. [Setting Up the GitHub Repository](#setting-up-the-github-repository)
3. [Configuring AWS ECR](#configuring-aws-ecr)
4. [Configuring IAM](#configuring-iam)
5. [Configure GitHub Secrets](#configure-github-secrets)
6. [Creating GitHub Actions Workflow](#creating-github-actions-workflow)
7. [Configuring ECS (Amazon Elastic Container Service)](#configuring-ecs-amazon-elastic-container-service)
   - [Configuring ECS Task Definition](#configuring-ecs-task-definition)
   - [Configuring ECS Cluster](#configuring-ecs-cluster)
   - [Configuring Service](#configuring-service)
8. [Final Output](#final-output)
## Overview

In this project, I explore the fundamental concepts of GitHub Actions pipeline. The goal is to push a Docker Python application image to AWS ECR (Elastic Container Registry) using GitHub Actions and deploy it to AWS ECS (Elastic Container Service).

## Steps

### 1. Setting Up the GitHub Repository

- Create a GitHub repository with the necessary files:
  - Dockerfile
  - Python.py
  - requirement.txt
  - Index.html
- Set up a GitHub Actions workflow (`main.yml`) to define the pipeline stages.
  - (.github/workflows/main.yml).

### 2. Configuring AWS ECR
### Steps

1. **Login to AWS Console:**
   - Open the [AWS Management Console](https://console.aws.amazon.com/).
   - Sign in with your AWS account credentials.

2. **Navigate to Amazon ECR:**
   - Go to `Services` and select `ECR` under the `Compute` section.

3. **Create a Private Repository:**
   - Click `Create repository`.
   - Name your repository.
   - Choose `Private` for repository visibility.
   - Optionally, configure tag mutability.
   - Click `Create repository`.
   - Note down the ECR repository Name and AWS region for later use.
     
## 3. Configuring IAM

Follow these steps to create an AWS Identity and Access Management (IAM) user with the necessary permissions to access the ECR repository and obtain the AWS access key ID and secret access key.

### Steps

- **Create IAM User:**
  - Navigate to the [IAM Management Console](https://console.aws.amazon.com/iam/).
  - Click on `Users` in the left sidebar.
  - Click on `Add user`.
  - Enter a username for the IAM user.
  - Select `Programmatic access` as the access type.
  - Click `Next: Permissions`.

- **Attach Policy:**
  - In the permissions section, select **Attach existing policies directly**.
  - Search for and select the policy named `AmazonEC2ContainerRegistryFullAccess`.
  - Click `Next: Tags`.

- **Tags (Optional):**
  - Optionally, add any tags if necessary.
  - Click **Next: Review**.

- **Review and Create User:**
  - Review the user details and attached policies.
  - Click `Create user`.

- **Access Key Creation:**
  - After the user is created, note down the **Access key ID** and **Secret access key**.
  - These credentials will be required to authenticate with AWS services(ECR).

     
## 4. Configure GitHub Secrets

Follow these steps to configure GitHub Secrets for your repository, allowing secure access to AWS services.

### Steps

- **Navigate to Repository Settings:**
  - Go to your GitHub repository.
  - Click on the **Settings** tab.

- **Access Secrets:**
  - In the left sidebar, select **Secrets** under the **Settings** section.
  - Click on **Actions** secrets.

- **Add New Secrets:**
  - Click on **New repository secret**.
  - Add the following three secrets:
    - `AWS_ACCESS_KEY_ID`: Set the value to the AWS access key ID obtained in Step 3.
    - `AWS_SECRET_ACCESS_KEY`: Set the value to the AWS secret access key obtained in Step 3.
    - `REPO_NAME`: Set the value to the name of your ECR repository.
    - `AWS_REGION`: Set the value to the AWS region where your ECR repository is located.
  - Click `Add secret` to save each secret.
    
## 5. Creating GitHub Actions Workflow

Follow these steps to create a GitHub Actions workflow for your repository. This workflow will automate the process of building and deploying your application using AWS credentials stored in GitHub Secrets.

### Steps

- **Create Workflows Directory:**
  - In your GitHub repository, create a new directory named `.github/workflows`.

- **Create YAML File:**
  - Inside the newly created `workflows` directory, create a new YAML file, for example, `main.yml`.

- **Define Workflow Structure:**
  - Define the workflow using the following structure:
    ```yaml
    name: Build Docker Image
    runs-on: ubuntu-latest  
    steps:  
        - name: Checkout
          uses: actions/checkout@v2    
          
        - name: Setup AWS ECR Details
          uses: aws-actions/configure-aws-credentials@v1
          with:
            aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
            aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
            aws-region: ${{ secrets.AWS_REGION}}


        - name: Login to Amazon ECR
          id: login-ecr
          uses: aws-actions/amazon-ecr-login@v1
          

        - name: Build and push the tagged docker image to Amazon ECR
          env:
            ECR_REGISTRY: ${{ steps.login-pf-aws-ecr.outputs.registry }}
            ECR_REPOSITORY: ${{secrets.AWS_ECR_REPO}}
            IMAGE_TAG: v6
          run: |
            docker build -t $ECR_REPOSITORY:$IMAGE_TAG .
            docker push $ECR_REPOSITORY:$IMAGE_TAG
      ```
- First check the indentation of your yml file is correct or not. Now,Let me explain the above code…
   - The starting section before `jobs` defines the name of the workflow as **Push the Docker image to AWS ECR Repo** and specifies that the workflow should be triggered when a push event occurs on the `main` branch.
   - Then we define job named `Build` that will run on ubuntu environment.
   - The first step is to check out the repository using the `actions/checkout` action. This step ensures that the repository's code is available for subsequent actions.
   - In the next step we configure AWS credentials by using `aws-actions/amazon-ecr-login`. The AWS access key ID and secret access key are obtained from the GitHub secrets we set earlier.
   - Then, we use the `aws-actions/amazon-ecr-login` action to authenticate with the Amazon ECR registry. This step generates and exports an environment variable called `REGISTRY` containing the ECR registry URL.
   - The last performs the actual build and push of the Docker image to the ECR repository. The ``docker build`` command builds the image from the **Dockerfile**.
## 6. Triggering the GitHub Workflow

Follow these steps to trigger the GitHub Actions workflow for your repository:

### Steps

1. **Commit and Push Changes**:
   - Make sure all your changes, including the `main.yml` file, are committed to your GitHub repository.
   - Push the changes to the `main` branch of your repository.

2. **Automated Trigger**:
   - GitHub Actions will automatically detect the changes pushed to the `main` branch and trigger the workflow defined in `main.yml`.

3. **Image Push to ECR Repo**:
   - The workflow will execute the defined steps, including building the Docker image and pushing it to your **AWS ECR repository**.
## 7. Configuring ECS (Amazon Elastic Container Service)

### Configuring ECS Task Definition

1. **Create Task Definition with Image URN**:
   - Navigate to `Task Definitions` in the **ECS dashboard**.
   - Choose `Create new Task Definition`.
   - Select the `launch type(Fargate)`.
   - Add the `container image URN` you pushed to Amazon ECR using GitHub Actions.
   - Configure `CPU`, `memory settings`, and `networking`.
   - `Review` and `create` the task definition.
     
### Configuring ECS Cluster

2. **Create ECS Cluster**:
    - Click on the `Create Cluster` button.
    - Choose the cluster template(e.g., EC2 or Fargate).
    - Provide a `name` for your cluster.
   **Configure Cluster Settings**:
    - Customize cluster settings such as `instance type`, `number of instances`, `networking`, and `security groups`.
   **Review and Create**:
    - Double-check all the configuration details.
    - Click on the `Create` button to create your ECS cluster.
   **Wait for Cluster Creation**:
    - Wait for the cluster creation process to complete.
3. **Configure Service**:

   - **Navigate to Cluster**:
     - Access your ECS cluster in the `ECS dashboard`.
   - **Access Services Section**:
     - Within the cluster details, locate and click on the `Services` section. 
   - **Create New Service**:
     - Click on `Create Service` to begin configuring a new service for your cluster. 
   - **Choose Task Definition and Launch Type**:
     - Select the appropriate task definition and launch type that previously created for the application. 
   - **Set Service Details**:
     - Configure service-specific details such as the number of tasks, `load balancing`, `auto-scaling`, and any other desired settings. 
   - **Review and Confirm**:
     - Double-check all configurations to ensure they align with application requirements.
     - Once satisfied, proceed to create the `service`.
   - **Service Creation**:
     - AWS will now create the service within your ECS cluster based on the provided configurations.

## 8. Final Output

After configuring the service in your ECS cluster, follow these steps to access and check your application:

1. **Service Execution**:
   - Once the service is created, ECS will initiate the tasks specified in the task definition.   
2. **Access Application**:
   - You can access your application using the following endpoints:
     - Load Balancer: [http://lb-5-314674044.us-east-1.elb.amazonaws.com:5000/](http://lb-5-314674044.us-east-1.elb.amazonaws.com:5000/)
     - Public IP: [http://44.204.40.178:5000/](http://44.204.40.178:5000/)
   
3. **Application Testing**:
   - Open your web browser and navigate to one of the provided URLs.
   - Verify that your application is running correctly and functioning as expected.
   - Test various functionalities to ensure proper operation.
By following these steps, you can validate the successful deployment and functionality of your application on Amazon ECS.
   

    




