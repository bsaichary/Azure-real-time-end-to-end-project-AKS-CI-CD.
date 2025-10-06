# Azure Real Time End-To-End-Project-AKS-CI/CD.
Complete End to End Azure Project with detailed guide for practice. (Aks, Docker, Azure container registry, CI/CD deployment through Argocd).

Project credit details are provided at the end. 

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


after completing the creation of virtual machine, setting up selfhost agent and installing docker then go on to follow step5.

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


once it is completed you can see the completed pipeline run in you pipeline section. Here you can see i have created pipelines for other two microservices.

<img width="947" height="403" alt="image" src="https://github.com/user-attachments/assets/c85362b3-1a25-4ca8-8c1e-80cf1d51efd1" />

similarly Now build the pipelines for other two microservices **vote** and **worker** by repeating the **Step5**

Now you can see all the images are build and pushed to Azure Container Registry.

To see them go to Azure Portal -> Container Registry -> Service (left side pane) -> Repositories -> your images (containers).

<img width="1854" height="775" alt="image" src="https://github.com/user-attachments/assets/07baabfc-acc3-4a59-8dae-304eb17351d2" />

## **Step6: Create Azure Kubernetes Service**

We will create a Azure Kubernetes cluster for deploying our application using ArgoCD.

In the Azure portal -> In search bar type Azure kubernetes cluster -> open kubernetes services.

<img width="1884" height="761" alt="image" src="https://github.com/user-attachments/assets/29c20021-d66f-485c-879e-6f8a34a91315" />


then on Click create -> then click on kubernetes cluster. 

<img width="1885" height="636" alt="image" src="https://github.com/user-attachments/assets/702ee82e-6914-4d08-a0b2-ce42e0e99264" />


provide Resource group name, kubernetes cluster name, location (can use default location also) if you face any resource limit error while creating then change the location and try again -> then in the node pool section choose click on the agent pool change the minimum and maximum node count to 1 and 2 -> in the optional settings enable the public ip per node -> then click on update -> then click review + create -> create. leave all other details default. it will take 2 -3 minutes to create it.


<img width="1124" height="815" alt="image" src="https://github.com/user-attachments/assets/126a7783-8b02-4f0a-bfd2-1e1cd1829056" />


## **Step7: Install Azure CLI and setu up AKS using CLI.

The Azure CLI is a tool we use to manage the Azure resources through command line (terminal).
Now, login to the linux virtual machine which we have created and install the Azure CLI, just copy and paste the below script and then click enter.

```
# Update the package list and install necessary dependencies
sudo apt-get update

# Install Azure CLI
echo "Installing Azure CLI..."
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

Now, from the Virtual machine login to your azure portal using the below command then follow the steps.

```
# Log in to Azure portal using command prompt
az login --use-device-code
```
once you execute this command it will prompt you to choose email id choose the one which you have used have used while creating azure fee subscription, then in the terminal it will show url and authentication code. take the url and browse it then copy paste the authentication code.

<img width="700" height="64" alt="image" src="https://github.com/user-attachments/assets/43ba936c-fe67-4edd-a5fd-4f67c37ea6bd" />

**Install kubectl** 
now install kubectl using command
```
sudo az aks install-cli
```

now login to AKS using command
```
az aks get-credentails --resource-group **resourcegroup** --name **aksname**
replace your Azure kubernetes resource group and kubernetes cluster name at above bold words.

to verify the connection to aks cluster use this command

**kubectl config current-context**
this command will show you to which aks cluster it is connected.
```

<img width="444" height="67" alt="image" src="https://github.com/user-attachments/assets/a392f45c-0880-40d8-b1de-547fa4020254" />

so, now we have successfully connected to our kubernetes cluster.

## **Step8: Install ArgoCD**

we will use a bash script to install argocd.
the bash script will first creates a namespace and then install argocd in the namespace then it will wait for argocd components to get ready, then it retrieves initail admin password for argocd later it exposes the argocd server to the internet using node port service which we can access via browser (url). and optionally it installs the argocd cli and logs in using the initial admin password.

create a file with name **argocd.sh** and copy paste the below content in to the file and execute the file.

```
#!/bin/bash

# Step 1: Install Argo CD
echo "Installing Argo CD..."

# Create a namespace for Argo CD
kubectl create namespace argocd

# Install Argo CD in the argocd namespace
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

# Step 2: Wait for all Argo CD components to be up and running
echo "Waiting for Argo CD components to be ready..."
kubectl wait --for=condition=Ready pods --all -n argocd --timeout=600s

# Step 3: Get the initial admin password
echo "Retrieving the Argo CD initial admin password..."
ARGOCD_INITIAL_PASSWORD=$(kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d)
echo "Argo CD initial admin password: $ARGOCD_INITIAL_PASSWORD"

# Step 4: Access the Argo CD server
echo "Exposing Argo CD server via NodePort..."
kubectl -n argocd patch svc argocd-server -p '{"spec": {"type": "NodePort"}}'

# Retrieve the Argo CD server URL
ARGOCD_SERVER=$(kubectl -n argocd get svc argocd-server -o jsonpath='{.spec.clusterIP}')
ARGOCD_PORT=$(kubectl -n argocd get svc argocd-server -o jsonpath='{.spec.ports[0].nodePort}')
echo "You can access the Argo CD server at http://$ARGOCD_SERVER:$ARGOCD_PORT"

# Step 5: Login to Argo CD CLI (Optional)
echo "Installing Argo CD CLI..."
sudo curl -sSL -o /usr/local/bin/argocd https://github.com/argoproj/argo-cd/releases/latest/download/argocd-linux-amd64
sudo chmod +x /usr/local/bin/argocd

echo "Logging into Argo CD CLI..."
argocd login $ARGOCD_SERVER:$ARGOCD_PORT --username admin --password $ARGOCD_INITIAL_PASSWORD --insecure

echo "Argo CD installation and setup complete!"
```

Script Overview:
Step 1: Installs Argo CD in a namespace called argocd.
Step 2: Waits for all Argo CD components to be ready.
Step 3: Retrieves the initial admin password for Argo CD.
Step 4: Exposes the Argo CD server using a NodePort service type, making it accessible via a URL.
Step 5: Optionally installs the Argo CD CLI and logs in using the initial admin password.

How to Use:
1. Save this script to a file, for example argocd.sh
2. Make the script executable: chmod +x argocd.sh
3. Run the script: ./argocd.sh
4. This will set up Argo CD on your Kubernetes cluster and provide you with the necessary details to access and manage it.

## **Step9: setup port rule for ArgoCD.
now run this command **kubectl get svc -n argocd** it will list out the services in the kubernetes. from the output we can see the nodeport port number that we should allow in azure vmss networking in inbound port rule then we can access it on the browser by using the IP address of our VM and the port address of port 80 service which is 31436 as shown below.

<img width="700" height="387" alt="image" src="https://github.com/user-attachments/assets/d520f5fe-7bb3-45b6-85ae-2f16fc95ec1d" />


<img width="700" height="116" alt="image" src="https://github.com/user-attachments/assets/7e918d5d-535e-4572-96b2-fb825cd744e2" />

## ** Step10: login to the ArgoCD**

go to browser and with your ip address and port number search it (example: http://52.250.58.123:31436)

<img width="700" height="453" alt="image" src="https://github.com/user-attachments/assets/a364336e-ee87-426a-86fa-db6fad090bc8" />


login = admin (default)
but password we need to fetch it from argocd installation of initial admin secret. follow the steps to get the password.

run this command in terminal
```
kubectl get secret -n argocd
```

<img width="566" height="158" alt="image" src="https://github.com/user-attachments/assets/b47a9438-f87a-49f4-8fea-da7ea5f3adca" />

now run this command in terminal
```
kubectl edit argocd-initial-admin-secret -n argocd
```

<img width="700" height="273" alt="image" src="https://github.com/user-attachments/assets/1c78ee50-35a4-4c0f-aac4-7d11b7ad6011" />

from this you will get the encoded password take it and keep it in the below command (<the argoCD password> replace this with password) and run it, it will give you actual password. (notedown the password somewhere)

```
echo <the argoCD password> | base64 --decode
```

now use this decrypted password and username admin to login to ArgoCD.
afer login, you will see something like this.

<img width="940" height="475" alt="image" src="https://github.com/user-attachments/assets/45dc738a-1564-42a5-b5f3-033a38b01941" />


## **Step11: Configure ArgoCD**

Now we have to configure the ArgoCD, so that it will always obverse the azure repo manifests files. when the configuration is completed it will automatically deploy the manifest files to kubernetes cluster, but then why it still observes it continusly because when there is a new change in application then it will deploy the new changes to cluster automatically. 

To configure the argocd, first we need to connect argocd to azure repo. 
first, take the clone url, 
Azure DevOps portal -> project -> repository -> click on clone -> copy the url.

<img width="1802" height="822" alt="image" src="https://github.com/user-attachments/assets/542f2aae-dc86-44ea-9d64-e1069f9b90d7" />

Now create a Pat token in azure devops.
devops portal -> user settings (top right corner) -> Personal access token -> click create new (top right corner) -> give a name -> check full access -> click create. 
note: notedown the token it will be visible only once.

<img width="1883" height="700" alt="image" src="https://github.com/user-attachments/assets/f18d96ec-fdcc-44e4-9f8c-e241a52dedd8" />

<img width="1797" height="818" alt="image" src="https://github.com/user-attachments/assets/171e8aa2-e0f5-4876-9300-5261542a916e" />

now, go to web browser argocd.
click settings -> click connect -> connection method choose via https/http -> project default -> repository url (this is the combinatio of pat token and clone url) -> click connect.

<img width="1916" height="566" alt="image" src="https://github.com/user-attachments/assets/369b16d7-0665-4d43-9ee3-64f084bae780" />

make sure the connection status should be successfull, otherwise ensure your are properly using pat token and clone url.

Now, ArgoCd -> Application (left side pane) -> new application -> give a name to application -> project select default -> sync policy choose automatic -> for repo url source choose azure repo url which we added earlier, click drop down it will appear -> path type k8s-specifications -> namespace choose default -> then click create. 
now the argocd will automatically deploys the manifest files to kubernetes cluster and you can see pods running from terminal using command **kubectl get pods -n argocd**

<img width="700" height="77" alt="image" src="https://github.com/user-attachments/assets/c567e258-9531-4156-9160-7289fed15205" />


the argocd will always watches this manifest files for any changes and it will deploy them.

<img width="700" height="333" alt="image" src="https://github.com/user-attachments/assets/08334844-0578-4384-b49d-9937a5322e53" />

now, after deploying we can see the pods are running (UI mode in argocd)

<img width="700" height="394" alt="image" src="https://github.com/user-attachments/assets/7b0a2301-2ad2-48b7-88fe-d3002ce6418c" />

till here we have completed the application deployment to azure kubernetes cluster.

## Step12: **Writing Bash Script**
Now as a devops engineer we need to make this into automation process for future changes to be auto updated, when you run the CI pipeline new containers will be created and pushed into Azure container registry, so then how the manifest files will be updated with new container infomation like **image name, image tag and deployment file prefix**  

So, here we will write a bash script and add that bash script in Azure devops repo -> vote -> file. This will do this tasks of updating image information in manifest files, from here argocd will take care.

```
#!/bin/bash

set -x

# Set the repository URL
REPO_URL="https://<ACCESS-TOKEN>@dev.azure.com/GabrielOkom/votingApp/_git/votingApp"

# Clone the git repository into the /tmp directory
git clone "$REPO_URL" /tmp/temp_repo

# Navigate into the cloned repository directory
cd /tmp/temp_repo

# Make changes to the Kubernetes manifest file(s)
# For example, let's say you want to change the image tag in a deployment.yaml file
sed -i "s|image:.*|image: <ACR NAME>/$2:$3|g" k8s-specifications/$1-deployment.yaml

# Add the modified files
git add .

# Commit the changes
git commit -m "Update Kubernetes manifest"

# Push the changes back to the repository
git push

# Cleanup: remove the temporary directory
rm -rf /tmp/temp_repo
```

## Step13: Update Deployment yaml file

We have to add new stage in deployment yaml file for bash script in vote-service pipeline.
Go to devops portal -> your project -> pipelines -> choose vote pipeline -> click edit -> add the below script at the bottom of the yaml pipeline.

```
# Docker
# Build and push an image to Azure Container Registry
# https://docs.microsoft.com/azure/devops/pipelines/languages/docker

trigger:
  paths:
    include: 
      - vote/*

resources:
- repo: self

variables:
  # Container registry service connection established during pipeline creation
  dockerRegistryServiceConnection: '26be9af5-3b2c-4b0b-a185-443a6868e826'
  imageRepository: 'vote'
  containerRegistry: 'projakscon.azurecr.io'
  dockerfilePath: '$(Build.SourcesDirectory)/vote/Dockerfile'
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

##################################
######### Add the script below to the vote-service pipeline

- stage: Update_bash_script
  displayName: update_Bash_script
  jobs:
  - job: Updating_repo_with_bash
    displayName: updating_repo_using_bash_script
    steps:
    - task: ShellScript@2
      inputs:
        scriptPath: 'vote/updateK8sManifests.sh'
        args: 'vote $(imageRepository) $(tag)'
    
```
Now, after adding the bash script in pipeline. We will go ahead and test our deployment.

## Step14: Updating Configmap yaml (optional).

NOTE: if the update script runs and not yet updated we can edit the configmap of the argocd to make fetch and deploy updates quicker, run kubectl edit cm argocd-cm -n argocd and add data: timeout-reconciliation to be 10s.

----------------------------------------------------------------------------------------------------
Example (optional step)
Edit argocd configmap yaml file kubectl edit cm argocd-cm -n argocd
Add the data: timeout-reconciliation section and make it look like this
```
apiVersion: v1
kind: ConfigMap
metadata:
  annotations:
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"v1","kind":"ConfigMap","metadata":{"annotations":{},"labels":{"app.kubernetes.io/name":"argocd-cm","app.kubernetes.io/part-of":"argocd"},"name":"argocd-cm","namespace":"argocd"}}
  creationTimestamp: "2024-08-26T21:20:54Z"
  labels:
    app.kubernetes.io/name: argocd-cm
    app.kubernetes.io/part-of: argocd
  name: argocd-cm
  namespace: argocd
  resourceVersion: "7318"
  uid: 6115ff37-28f1-4c0b-a477-b43f827cbb94
data:
  timeout.reconciliation: 10s
```

<img width="700" height="99" alt="image" src="https://github.com/user-attachments/assets/909c5c6c-47be-415f-8803-eecab326ed87" />

<img width="700" height="185" alt="image" src="https://github.com/user-attachments/assets/228b8a14-f85d-472c-b771-97e3b731a4b8" />

Note: For data.reconciliation in production using 10s is not ideal; the recommended is atleast 180s which is 3mins this is to enable ArgoCD have enough time to update and sync without giving too much pressure to the services.  

## Step15: check pods and troubleshoot for any errors

Check the status of pods using command **kubectl get pods** 

<img width="681" height="147" alt="image" src="https://github.com/user-attachments/assets/75de10ff-1763-4aed-b0e2-ff137ab7c1c5" />


If you see any errors with respect to images like imagepullbackoff. 
then look for which image is showing the error, then take that image name and run the describe command to get the complete details 
command **kubectl describe pod <image name>**

You can also check the error in ArgoCD UI 

<img width="700" height="385" alt="image" src="https://github.com/user-attachments/assets/e341e23e-5a81-413d-a830-da6c4e7d950a" />

## Step16: Create Imagepullsecret on AKS

Here the Kubernetes is not able to pull the latest changes from Azure container registry hence showing imagepullbackoff error. 
To resolve this error we need to enable the admin user in azure container registry.
Go to -> Azure Portal -> Azure container Registry -> settings -> access key -> check mark the admin user box and then copy the password.

<img width="1881" height="794" alt="image" src="https://github.com/user-attachments/assets/d8c0d12a-ea74-43f5-b046-8b3f25581d6e" />

Now run this command with inserting the details.
give a 
secret-name
namespace
insert the below details
container-registry-name
service-principal-ID
service-principal-password (password from container registry)
```
kubectl create secret docker-registry <secret-name> \
    --namespace <namespace> \
    --docker-server=<container-registry-name>.azurecr.io \
    --docker-username=<service-principal-ID> \
    --docker-password=<service-principal-password>
```

<img width="700" height="94" alt="image" src="https://github.com/user-attachments/assets/8a440bf5-cc30-4e53-979f-5164b3ca42c8" />


Now go to vote deployment file and add newly created Azure container secret.
Go to -> Azure devops -> repo -> click k8s-specifications -> edit the vote-deployment.yaml and add the imagepullsecret section inside of it with the name of the secret created earlier then click on commit changes.

<img width="700" height="389" alt="image" src="https://github.com/user-attachments/assets/873d50c9-d821-41a1-a006-6a1d13630312" />

Now run the kubectl get svc command for getting the port number and kubectl get pods to check the status of pods running. 
To access the page use the same IP address which you have used for accessing the argocd and map it with 3100 port (your port number might differ with mine). and ensure you have allowed this 31000 port number in networking of vmss, as similarly we have done it for argocd port number. (http://<nodeip-address>:31000).

<img width="700" height="119" alt="image" src="https://github.com/user-attachments/assets/196b4276-cf94-4fb0-9ead-a3ea2ee92ad8" />

once you access the page you will see like this.

<img width="700" height="467" alt="image" src="https://github.com/user-attachments/assets/771655b7-5cc4-4e6e-85c9-93b7cff0e2d2" />

## **Step17: Now Lets check the CICD process**

go to -> devops -> Repo -> vote -> edit os.getenv section of app.py file. now try to change the votes to something like this (summer, winter) (Rain, Snow) etc. then commit the changes.  

```
from flask import Flask, render_template, request, make_response, g
from redis import Redis
import os
import socket
import random
import json
import logging
option_a = os.getenv('OPTION_A', "Rain")
option_b = os.getenv('OPTION_B', "Snow")
hostname = socket.gethostname()
app = Flask(__name__)
gunicorn_error_logger = logging.getLogger('gunicorn.error')
app.logger.handlers.extend(gunicorn_error_logger.handlers)
app.logger.setLevel(logging.INFO)
def get_redis():
    if not hasattr(g, 'redis'):
        g.redis = Redis(host="redis", db=0, socket_timeout=5)
    return g.redis
@app.route("/", methods=['POST','GET'])
def hello():
    voter_id = request.cookies.get('voter_id')
    if not voter_id:
        voter_id = hex(random.getrandbits(64))[2:-1]
    vote = None
    if request.method == 'POST':
        redis = get_redis()
        vote = request.form['vote']
        app.logger.info('Received vote for %s', vote)
        data = json.dumps({'voter_id': voter_id, 'vote': vote})
        redis.rpush('votes', data)
    resp = make_response(render_template(
        'index.html',
        option_a=option_a,
        option_b=option_b,
        hostname=hostname,
        vote=vote,
    ))
    resp.set_cookie('voter_id', voter_id)
    return resp

if __name__ == "__main__":
    app.run(host='0.0.0.0', port=80, debug=True, threaded=True)
```

after commiting the changes pipeline will auto trigger. 

<img width="700" height="336" alt="image" src="https://github.com/user-attachments/assets/418cc452-724e-4367-8a37-d270dddb5da8" />

Once the pipeline running is completed, go to website and check the changes (refres it) you will see the new changes. 

<img width="700" height="297" alt="image" src="https://github.com/user-attachments/assets/2d0e9b49-f3ce-4dc9-8802-4330a6defed9" />

## The End 

But make sure to do the pipeline for other microservices ((worker and result) also because when you start do it then only you will able to understand.

**The project is originally from NotHarshhaa**

I would like to convey my heartfull thanks to NotHarshhaa for the project, because i have learned from this project. and I have used some of his images in this project aswell. but I have used it only for the purpose of Learning and I have provided my version of this project. 

Anyway guys try to do this project completely and if you get any errors get stop there i know it will take time but use ai tools (chatgpt or copilot) for faster resolution, and for freshers, developer who are willing to come to devops field this project will definitly give you lot of knowledge and hands on experience. 

Good Luck - Have a Great Learning.
