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

below is the detailed step by step guide.


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


## **Step2: Import GitHub repo to Azure Repo**:

Go to your project -> Click Repos -> click Import -> copy & paste this url: https://github.com/dockersamples/example-voting-app.git -> click Import.

<img width="1805" height="834" alt="image" src="https://github.com/user-attachments/assets/dcd82c3a-e400-4bd6-8680-1ba600b75305" />

Once your click Import the code will be imported into your Azure repos from GitHub.

<img width="1788" height="853" alt="image" src="https://github.com/user-attachments/assets/1c711684-a1ac-4da2-bd4f-31009024ac0b" />

## **Step3: Create Azure Container Registry**

We will store our Docker Containers in this Registry after Building them using CI Pipeline in Azure DevOps.

Login to Azure Portal url: https://portal.azure.com 
Search Container registry -> Click Create 

<img width="700" height="332" alt="image" src="https://github.com/user-attachments/assets/7fb4f86b-0244-44fc-b18c-6514e987b6ec" />

Provide details: Resource group name, Registry name, location, Click Review + create, create. (Rest all leave for default values).

<img width="713" height="423" alt="image" src="https://github.com/user-attachments/assets/673b7b0b-3c44-408a-a83d-b0c05cfbe2f1" />

Go to your created container registry -> settings -> access key -> check mark the admin user. 

<img width="1546" height="801" alt="image" src="https://github.com/user-attachments/assets/fbf2afad-68cf-4312-be79-be35e3f6b33b" />


## **Step4: Stepup Self Hosted Agent for running pipeline**

First we will create a linux virtual machine then we will setup the self hosted agent and also install docker as it is a dependency for building images.  
Steps to create vm use this link https://github.com/bsaichary/Azure-Linux-Virtual-Machine-Creation-detail-guide/blob/main/README.md 


after creation of virtual machine, setting up selfhost agent and installing docker follow step5.

## **Step5: Building CI Pipeline in azure devops

With this CI pipeline we are going to build the code and create docker images using DockerFile which is present in Repository and finally pushing those created images to Azure Container Registry.

To create a CI Pipeline go to **Azure DevOps portal** -> **your organization** (red box mark) -> **your project** (green box mark) -> on the left side pane click on **pipelines** -> click on **create pipeline**


<img width="1903" height="875" alt="image" src="https://github.com/user-attachments/assets/d5d12302-a558-40e3-9991-fe8a6db359e4" />


now click on **Azure Repo Git yaml** this means we are choosing our Azure devops repository where we have imported the code in **Step2** and we are using yaml based pipeline.

<img width="1919" height="885" alt="image" src="https://github.com/user-attachments/assets/a7c64135-cbc8-4dce-afb0-79c87657683d" />


Now choose your **Repository** 

<img width="1913" height="896" alt="image" src="https://github.com/user-attachments/assets/e505fc67-1e3b-4f15-a62e-a274aba80f19" />


now choose **Docker** (**Build and push** an image to Azure Container Registry) -> then choose or check mark the **Azure Subscription** -> then click on **continue**. 

<img width="1788" height="817" alt="image" src="https://github.com/user-attachments/assets/181b51f4-afbf-4308-bf41-9077e3f86d46" />


now choose your **Container Registry** from drop down which you have created in **Step3** -> then give a name to **image** or leave **default** -> click validate and configure. 

Here look at DockerFile this tells us that we are building result microservice image $(Build.SourceDirectory/**result**/DockerFile). So, you have to change the microservice when you are created another pipelines for worker and vote respectively. Hence you have to create two more pipelines similarly later after this pipeline.

<img width="1805" height="826" alt="image" src="https://github.com/user-attachments/assets/1a119a76-305a-4a11-bfaf-09dc3f4acfaf" />


Now you see the yaml pipeline which is auto generated by Azure but i have done some changes like adding the agent vm name, and i have used two stages one for build and another for push. you can use this yaml also. but ensure you add your own self host agent (Agent VM image name).

```
trigger:
  paths:
    include: 
      - result/*

resources:
- repo: self

variables:
  # Container registry service connection established during pipeline creation
  dockerRegistryServiceConnection: '26be9af5-3b2c-4b0b-a185-443a6868e826'
  imageRepository: 'result'
  containerRegistry: 'projakscon.azurecr.io'
  dockerfilePath: '$(Build.SourcesDirectory)/result/Dockerfile'
  tag: '$(Build.BuildId)'

  # Agent VM image name
pool: 
  name: Abhilash
  demands:
  - agent.name -equals projagent

stages:
- stage: Build
  displayName: Build the voting app
  jobs:
  - job: Build
    displayName: Build
    pool: 
      name: Abhilash
      demands:
       - agent.name -equals projagent
    steps:
    - task: Docker@2
      inputs:
        containerRegistry: $(dockerRegistryServiceConnection)
        repository: $(imageRepository)          
        command: 'build'
        Dockerfile: $(dockerfilePath)

- stage: Push                           
  displayName: Push the Voting App        
  jobs:
  - job: Push
    displayName: Pushing the voting App
    steps:
    - task: Docker@2
      inputs:
        containerRegistry: $(dockerRegistryServiceConnection)
        repository: $(imageRepository)
        command: 'push' 
```

Now click on save and run -> click save and run again. The pipeline will start running, 

<img width="700" height="139" alt="image" src="https://github.com/user-attachments/assets/7300e82c-74bc-49ed-8320-d83dc0ad5581" />


once it is completed you can see the completed pipeline run in you pipeline section. i have created all the pipelines.

<img width="947" height="403" alt="image" src="https://github.com/user-attachments/assets/c85362b3-1a25-4ca8-8c1e-80cf1d51efd1" />

similarly Now build the pipelines for other two microservices **vote** and **worker** by repeating the **Step5**

Now you can see all the images are build and pushed to Azure Container Registry.
Azure Portal -> Container Registry -> Service (left side pane) -> Repositories -> your images (containers).

<img width="1854" height="775" alt="image" src="https://github.com/user-attachments/assets/07baabfc-acc3-4a59-8dae-304eb17351d2" />














































