# Arm-Template-Notes
```
IMPORTANT: Please follow the below instructions sequencly to deploy the ARM template !!

Note 1: Customize the aks.json file with the desired parameter 

Note 2: Template will create the cluster private and you can make it public by making the below parameter 
in aks.json "false"

"apiServerAccessProfile": {
        "enablePrivateCluster": true
                            },

Private Cluster: Without Public API Serve and must be managed from jump server

Public Cluster: With Public API Server and can be managed directly
```

# Pre Config on Azure Portal
```
1- Create Resource Group for AKS System name "aks-dev-elemam"

2- Create Resource Group for DNS name "Azure-DNS"

3- Create Resource Group for Network name "Network"

4- Create Resource Group for Storage name "Storage" and Create Storage Account from Cloud Shell

5- Create App Service domain from Azure or use your Domain

6- Create DNS Zone elemamm.developers.global.io #developers.global.io this is my domain name

7- Create in RG "Network" Virtual Network name "VN_EastUS"

8- Create in VN "VN_EastUS" 2 Subnets names "SN-AKS-System" and "SN-AKS-Workload"
```

# Deploy the ARM Template From CloudShell
```
Note: Template URI must be in a public repo 

PS /home/mohamed> New-AzResourceGroupDeployment -Name d1 -ResourceGroupName aks-dev-elemam -mode Incremental 
-TemplateUri https://raw.githubusercontent.com/EngMohamedElEmam/azure/master/arm-template/templates/azuredeploy.json 
-TemplateParameterFile ./aks.json -verbose
```

# Install AKS CLI 
```

PS /home/mohamed> az aks install-cli

#Add the AKS preview extension

PS /home/mohamed> az extension add --name aks-preview
```

# Get and store the credentials for the AKS cluster
```

PS /home/mohamed> az aks get-credentials --resource-group aks-dev-elemam --name aks-dev-elemam-01 --admin --overwrite-existing
```

# Switch to bash then run iam.sh 
```

mohamed@Azure:~$ sh iam.sh

After Deploying ARM template will use script create-cluster.sh to create the cluster from cluster services template and 
modify create-cluster.sh line 31 and 52 for the correct path for repo working directory 

Check the following instructions: https://github.com/EngMohamedElEmam/GitOps-FluxCD/tree/main/setup
```

# Manage Private Cluster From Lens
```

1- Create Jump Server and access with MobaXterm 

2- run the following commands:

root@Jumpbox:~# az login

root@Jumpbox:~# az aks get-credentials --resource-group rg-prd-aks --name aks-prd-eastus-01 --overwrite-existing

3- Copy the config file for the cluster to your local machine with the below command from your local machine:

C:\Users\elemamm> scp root@elemamjumpserver.eastus.cloudapp.azure.com:/root/.kube/config C:\Users\elemam\.kube

4- Open config file from your local machine with any editor and edit the private link from 
"server: https://aks-dev-elemam-01-cfdb436a.4e05ee57-4767-4032-ac05-27a144678e6f.privatelink.eastus.azmk8s.io:443" 
to be "server: https://localhost:6443"

5- Create a new SSH tunnel from MobaXterm with the below parameters:

"local client port: 6443" 

"SSH Server: elemamjumpserver.eastus.cloudapp.azure.com (DNS NAME OF JUMPSERVER) and username emam and port 22"

"Remote Server: IP (from az portal > rg "aks-dev-elemam-01-nodes" > kube-apiserver.nic (10.0.2.4)) Port (443)

6- Install lens into your local machine

7- Open lens and add a cluster  
```
