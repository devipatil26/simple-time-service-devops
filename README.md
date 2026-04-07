SimpleTimeService

1. &#x20;Overview



This project demonstrates an end-to-end DevOps workflow by building a microservice and deploying it using modern DevOps practices.



The implementation covers:



Application development

Containerization using Docker

Deployment using Kubernetes

Infrastructure provisioning using Terraform (AWS EKS)



The focus was to create a clean, production-like setup with proper structure, security, and reproducibility.

\------------------------------------------------------------------------------------------------------------------------------------------

2\. Tech Stack

| Tool             | Purpose                         |

|------------------|---------------------------------|

| Python (Flask)   | Backend microservice            |

| Docker           | Containerization                |

| Kubernetes       | Container orchestration         |

| Terraform        | Infrastructure provisioning     |

| AWS (EKS, VPC)   | Cloud platform                  |

\------------------------------------------------------------------------------------------------------------------------------------------

3\. Project Structure

.

├── app/                # Application code

│   ├── app.py

│   ├── requirements.txt

│   └── Dockerfile

├── kubernetes/         # Kubernetes manifests

│   └── microservice.yml

├── terraform/          # Terraform configuration

│   ├── main.tf

│   ├── variables.tf

│   ├── terraform.tfvars

│   └── providers.tf

└── README.md

\------------------------------------------------------------------------------------------------------------------------------------------

4\. Application Details



The service exposes a single endpoint:



GET /

Sample Response

{

&#x20; "timestamp": "2026-04-07T20:30:00",

&#x20; "ip": "client IP address"

}



Explanation

timestamp → current server time in ISO format

ip → IP address of the incoming request



Step 1: Run Application Locally

Install dependencies

pip install -r requirements.txt



\-- Installs all required Python packages defined in requirements.txt

\-- Ensures the application runs consistently across environments



Start the application

python app.py



\-- Starts the Flask server

\-- Application runs on 0.0.0.0:8080

\-- 0.0.0.0 allows external access (important for Docker/Kubernetes)



Test the application

curl http://localhost:8080



\-- Sends an HTTP request to the application

\-- Returns JSON response with timestamp and IP

\------------------------------------------------------------------------------------------------------------------------------------------

Step 2: Docker Setup

Build Docker image

docker build -t devyanipatil26/simpletimeservice .



\--  Builds a Docker image using the Dockerfile

\-- -t assigns a name (tag) to the image

\-- . means current directory is used as build context



Run Docker container

docker run -p 8080:8080 devyanipatil26/simpletimeservice



\-- Runs the container locally

\-- -p 8080:8080 maps:

Host port (laptop)

Container port (application)


Push image to DockerHub

docker push devyanipatil26/simpletimeservice



\-- Uploads the image to DockerHub

\-- Required so Kubernetes can pull the image

\------------------------------------------------------------------------------------------------------------------------------------------

Step 3: Kubernetes Deployment

Apply manifest

kubectl apply -f kubernetes/microservice.yml



\-- Creates Kubernetes resources:



Deployment → manages pods

Service → exposes application internally



Check pods

kubectl get pods



\-- Displays running pods

\-- Should show 2 replicas (as defined in deployment)



Check service

kubectl get svc



\-- Shows service details

\-- Confirms networking is configured



Access application

kubectl port-forward svc/time-service 8080:80



\-- Forwards traffic from local machine to Kubernetes service

\-- Allows local testing without LoadBalancer



curl http://localhost:8080



\-- Verifies application is working inside Kubernetes

\------------------------------------------------------------------------------------------------------------------------------------------

Step 4: Terraform (Infrastructure as Code)



Terraform is used to provision AWS infrastructure.



\--Configure AWS CLI (Details ahead)

aws configure



\-- Stores AWS credentials locally

\-- Required for Terraform to authenticate with AWS



Terraform Commands

\--Initialize Terraform

terraform init



\-- Downloads required providers and modules

\-- Prepares working directory



Validate configuration

terraform validate



\-- Checks syntax and configuration correctness

\-- Helps catch errors before execution



Generate execution plan

terraform plan



\-- Shows what Terraform will create

\-- No resources are created

\-- Used to verify infrastructure before deployment



Note-

terraform apply



\--This command creates actual AWS resources



To avoid unnecessary cost, this step was intentionally not executed.

However, the configuration is complete and production-ready.

\------------------------------------------------------------------------------------------------------------------------------------------

Configure AWS Credentials

aws configure

Provide:
AWS Access Key ID: <your-access-key>

AWS Secret Access Key: <your-secret-key>

Default region name: ap-south-1

Default output format: json



Verify Authentication

aws sts get-caller-identity


Expected output - json

\------------------------------------------------------------------------------------------------------------------------------------------

Security \& Best Practices



1\. Non-root container execution



The application container is configured to run as a non-root user instead of the default root user.



Why this matters:

\- Reduces security risks in case of container compromise  

\- Follows the principle of least privilege  

\- Prevents unauthorized access to system-level resources  



2\. Lightweight Docker image



A minimal base image (`python:3.9-slim`) is used to build the container.



Benefits:

\- Smaller image size → faster builds and deployments  

\- Reduced attack surface  

\- Improved performance in CI/CD pipelines  



3\. Kubernetes high availability



The application is deployed using a Kubernetes Deployment with multiple replicas.



Advantages:

\- Ensures high availability  

\- Enables load distribution across pods  

\- Supports self-healing (failed pods are automatically recreated)  



4\. Infrastructure as Code (Terraform modules)



Reusable Terraform modules are used for provisioning AWS resources such as VPC and EKS.



Why this is important:

\- Promotes modular and maintainable code  

\- Ensures consistent infrastructure across environments  

\- Simplifies updates and scaling  



5\. Secure handling of credentials



No sensitive information (AWS keys, secrets, tokens) is stored in the repository.



Practices followed:

\- AWS credentials configured via AWS CLI (`aws configure`)  

\- `.gitignore` used to prevent accidental commits  

\- Secrets managed outside the codebase  



6\. Controlled infrastructure deployment



Infrastructure changes are validated using:

terraform plan
------------------------------------------------------------------------------------------------------------------------------------------

Version Control (Git)
The project is version-controlled using Git and hosted on a public repository.


Step 1: Initialize repository

git init


\-- Initializes a new Git repository in the project directory



Step 2: Add files to staging

git add .



\-- Adds all project files to the staging area

\-- Prepares files to be committed



Step 3: Commit changes

git commit -m "Initial commit - DevOps project setup"



\-- Saves the current state of the project

\-- Commit message describes the changes



Step 4: Connect to remote repository

git remote add origin https://github.com/devipatil26/simple-time-service-devops.git



\-- Links local repository to GitHub repository



Step 5: Push code to GitHub

git branch -M main

git push -u origin main



\-- Uploads code to remote repository

\-- Sets main branch as default



Step 6: Update changes

git add .

git commit -m "Updated project"

git push



\-- Used for pushing future updates



Why Git is used

Tracks changes in the project

Enables collaboration

Maintains version history

Allows easy rollback if needed

\------------------------------------------------------------------------------------------------------------------------------------------

Key Highlights



\- Built a minimal microservice and exposed a REST endpoint  

\- Containerized the application using Docker with non-root user configuration  

\- Deployed the application on Kubernetes using Deployment and Service  

\- Designed AWS infrastructure (VPC + EKS) using Terraform modules  

\- Validated infrastructure using `terraform plan` without incurring cloud costs  

\- Structured the project for clarity, reusability, and ease of deployment  

\- Provided detailed documentation for end-to-end setup and execution 

\------------------------------------------------------------------------------------------------------------------------------------------

Conclusion



This project demonstrates a complete end-to-end DevOps workflow, starting from building a minimal microservice to deploying it using containerization, Kubernetes orchestration, and Infrastructure as Code with Terraform.



It highlights practical experience in:

\- Designing and developing a lightweight service  

\- Packaging applications into secure and optimized Docker containers  

\- Deploying and managing workloads in Kubernetes  

\- Defining and validating cloud infrastructure using Terraform  



The project also emphasizes best practices such as security (non-root containers), modular infrastructure design, and clear documentation to ensure reproducibility.



Overall, this solution reflects a production-oriented approach to building and managing modern cloud-native applications.

