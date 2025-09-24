# Azure Real Time End-To-End-Project-AKS-CI-CD.
This project deals with Aks, Docker, Azure container registry, CI/CD deployment through Argocd. This Repo includes detailed practical guide for those who are looking to practice complete end to end real time project.

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

Now Let's get started.



