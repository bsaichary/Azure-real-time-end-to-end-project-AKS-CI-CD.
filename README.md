# Azure-end-to-end-project-AKS-CI-CD.
This project deals with Aks, Docker, Azure container registry, CI/CD deployment through Argocd. This Repo includes detailed practical guide for those who are looking to practice complete end to end real time project.

Steps:
1. Create a Project in Azure DevOps Organization.
2. Clone/Import the Repository from GitHub to Azure Repository (using clone url).
3. Create Azure Container Registry in Azure portal.
4. Create a Linux Ubuntu Virtual machine for using it as a self hosted agent to run the CI/CD pipeline.
6. Build the CI pipeline for each microservice (result, worker, vote) using (Docker build and push).
7. create a Azure Kubernetes Cluster.
8. Login to Azure through linux virtual machine using ssh or username or password of vm.
9. Login to AKS (az aks get-credentials --resource-group "resource_group_name" --name "AKS_cluster")
10. 
