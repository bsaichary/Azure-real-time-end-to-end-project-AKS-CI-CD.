# Azure Real Time End-To-End-Project-AKS-CI-CD.
Complete End to End Azure Project with detailed guide for practice. (Aks, Docker, Azure container registry, CI/CD deployment through Argocd).

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
14.   -> Configure Azure Repository with ArgoCD.
15.   -> Connect AKS with ArgoCD. (ArgoCd will auto deploy the code to cluster once connected)
16. Writing bash script to auto update the manifest files with latest image builds through CI.

# Now Let's get started.
The Architecture diagram of the Project.

<img width="700" height="334" alt="Architecture diagram" src="https://github.com/user-attachments/assets/897119d2-f5fe-4139-8b54-727454d6b602" />

**Project Description**
This is a Micro-service based application used for Voting Purpose where the users will cast their votes through web interface
Tech Stacks:
Python
Node.js
Redis
PostgreSQL.




