# Azure Real Time End-To-End-Project-AKS-CI/CD.
Complete End to End Azure Project with detailed guide for practice. (Aks, Docker, Azure container registry, CI/CD deployment through Argocd).

**Project Description**
This project is a microservices-based voting app built with Docker and deployed on Azure Kubernetes Service (AKS) using ArgoCD. It demonstrates a full CI/CD pipeline where code changes trigger container builds, push to Azure Container Registry, and automatic deployments to AKS. The app uses Redis, PostgreSQL, Python, Node.js, and .NET services to process votes and display real-time results.

The Architecture diagram of the Project.

<img width="700" height="334" alt="Architecture diagram" src="https://github.com/user-attachments/assets/897119d2-f5fe-4139-8b54-727454d6b602" />

Steps:
1. Create a Project in Azure DevOps Organization.
2. Clone/Import the Repository from GitHub to Azure Repository (using clone url).
3. Create Azure Container Registry in Azure portal.
4. Create a Linux Ubuntu Virtual machine for using it as a self hosted agent to run the CI/CD pipeline.
6. Build the CI pipeline for each microservice (result, worker, vote) using (Docker build and push).
7. Create a Azure Kubernetes Cluster(AKS).
8. Install Azure CLI in your local machine.
9. Install Azure AKS CLI.
10. Login to Azure Portal using az login --use-device-code.
11. Login to AKS (az aks get-credentials --resource-group "resource_group_name" --name "AKS_cluster")
12. Install ArgoCD package using provided bash Script.
13. Login to ArgoCD.
14. Configure Azure Repository with ArgoCD.
15. Connect AKS with ArgoCD. (ArgoCd will auto deploy the code to cluster once connected)
16. Writing bash script to auto update the manifest files with latest image builds through CI.

# Now Let's get started.

**Prequisites:**
1. Azure Free trail Subscription
2. Curiosity to Learn DevOps
3. Basic Knowledge of Linux Commands and Azure DevOps. 


## **Step1: Create Azure DevOps Organization & Project**
Login to your Azure DevOps portal url: https://dev.azure.com then create a Organization and Project.
if your creating a project for first time then first you need to create a Organization because your project will be inside the organization.

**Create Organization**: 
click on New Organization -> Click Continue -> give name to Organization & enter captcha -> click continue.

<img width="1915" height="834" alt="image" src="https://github.com/user-attachments/assets/dbacb6d2-8b15-4065-b240-ae29878d649a" />


Once your done creating organization it will look as shown in below highlighted screenshot. 

**Create Project**: 
Give name to Project -> Click create.

<img width="1913" height="866" alt="image" src="https://github.com/user-attachments/assets/1b7ce79a-9e0a-4b3d-b7dc-f5c77feeef03" />


Once your Project is created it will look like this.


<img width="1912" height="877" alt="image" src="https://github.com/user-attachments/assets/280ddac1-0f59-45b5-ad55-21dcf7ddc364" />


**Import GitHub repo to Azure Repo**:

Go to your project -> Click Repos -> click Import -> copy & paste this url: https://github.com/dockersamples/example-voting-app.git -> click Import.

<img width="1805" height="834" alt="image" src="https://github.com/user-attachments/assets/dcd82c3a-e400-4bd6-8680-1ba600b75305" />

Once your click Import the code will be imported into your Azure repos from GitHub.

<img width="1788" height="853" alt="image" src="https://github.com/user-attachments/assets/1c711684-a1ac-4da2-bd4f-31009024ac0b" />



















