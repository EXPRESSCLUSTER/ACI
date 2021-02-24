# Deploy clpwitnessd with ACI

## Index
- [Prerequisite](#prerequisite)
- [Create a Container Registry on Azure](#create-a-container-registry-on-azure)
- [Pull and Push Container Image](#pull-and-push-container-image)
- [Deploy Witness Server](#deploy-witness-server)
- [Set Witness Server to Cluster Properties](#set-witness-server-to-cluster-properties)
- [Link](#link)


## Prerequisite
- Azure account.
- Linux machine to get a Docker image.

## Create a Container Registry on Azure
- Refer to the following steps to create a registry.
  - https://github.com/EXPRESSCLUSTER/App-Service/blob/main/DeployCWO.md#create-a-container-registry-on-azure

## Pull and Push Container Image
1. Pull clpwitnessd container image to your Linux machine.
   ```sh
   # docker pull docker.io/expresscluster/clpwitnessd:4.2.0
   ```
1. Change the tag as below.
   ```sh
   # docker tag docker.io/expresscluster/clpwitnessd:4.2.0 <your registry name>.azurecr.io/clpwitnessd:4.2.0
   ```
1. Login to your registry.
   ```sh
   # docker login <your registry name>.azurecr.io --username <your user name>
   Password: <enter your password>
   ```
1. Push the Cluster WebUI Offline container image.
   ```sh
   # docker push <your registry name>.azurecr.io/clpwitnessd:4.2.0
   ```

## Deploy Witness Server
1. Click **Container instances** icon and click **Create**.
1. Set the following parameters and create the container.
   - Basics
     - Resource group: your resource group
     - Container name: your application name (E.g. clpwitness)
     - Region: your region
     - Image source: Azure Container Registry 
     - Registry: your registry
     - Image: clpwitnessd
     - Image tag: select tag
   - Networking
     - Networking type: Public
     - DNS name label: clpwitness-420
     - Ports: 80

## Set Witness Server to Cluster Properties
1. Start Cluster WebUI and change **Config** mode.
1. Open **Cluster Properties** and click **Interconnect** tab.
1. Click **Add** and select **Witness**.
1. Click **Properties** and set the following parameter.
   - Target Host: clpwitness-420.japaneast.azurecontainer.io
   - Service Port: 80
   - See also; 
     - https://www.manuals.nec.co.jp/contents/system/files/nec_manuals/node/504/W42_RG_EN/W_RG_02.html#interconnect-tab
     - https://www.manuals.nec.co.jp/contents/system/files/nec_manuals/node/505/L42_RG_EN/L_RG_02.html#interconnect-tab 

## Link
- Witness Server
  - The following containers run with Fukunaga's Visual Studio account. If the cost reaches the limit, the containers stop.
    - http://clpwitness-420.japaneast.azurecontainer.io/
- Migrate custom software to Azure App Service using a custom container
  - https://docs.microsoft.com/en-us/azure/app-service/tutorial-custom-container?pivots=container-linux