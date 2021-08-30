# Deploy clpwitnessd with ACI

## Index
- [Overview](#overview)
- [Prerequisite](#prerequisite)
- [Deploy Witness Server](#deploy-witness-server)
- [Set Witness Server to Cluster Properties](#set-witness-server-to-cluster-properties)

## Overview
- The cluster nodes are running on availability zone 1 and 2.
- The witness server runs on a different subnet.
  ```
             +-------------------------+
             | Container Instance      |
             | clpwitnessd             |
             | 10.0.1.4/24             |
             +------------+------------+
                          |
             +------------+------------+
             |                         |
  +----------+----------+   +----------+----------+
  | Virtual Machine     |   | Virtual Machine     |
  | Availability zone 1 |   | Availability zone 2 |
  | centos01            |   | centos02            |
  | 10.0.0.6/24         |   | 10.0.0.7/24         |
  +---------------------+   +---------------------+
  ```

## Prerequisite
- Create subnet (e.g. Name: witnessd, IP Address: 10.0.1.0/24) in advance.

## Deploy Witness Server
1. Click **Container instances** icon and click **Create**.
1. Set the following parameters and create the container.
   - Basics
     - Resource group: your resource group
     - Container name: your application name (E.g. clpwitnessd)
     - Region: your region
     - Image source: Docker Hub or other registry
     - Image type: Public
     - Image: expresscluster/clpwitnessd
       - Fore more detail, please visit: https://hub.docker.com/r/expresscluster/clpwitnessd.
     - OS type: Linux
   - Networking
     - Networking type: Private
     - Virtual network: your virtual network
     - Subnet: your subnet that [you created in advance](#prerequisite)
     - Ports: 80
   - Advanced
     - Restart policy: OS failure
   - Tags
     - Set your own tags.

## Set Witness Server to Cluster Properties
1. Start Cluster WebUI and change **Config** mode.
1. Open **Cluster Properties** and click **Interconnect** tab.
1. Click **Add** and select **Witness**.
1. Click **Properties** and set the following parameter.
   - Target Host: private IP address of the container instance (e.g. 10.0.1.4).
   - Service Port: 80
   - See also; 
     - Windows: https://www.manuals.nec.co.jp/contents/system/files/nec_manuals/node/539/W43_RG_EN/W_RG_02.html#interconnect-tab
     - Linux: https://www.manuals.nec.co.jp/contents/system/files/nec_manuals/node/540/L43_RG_EN/L_RG_02.html#interconnect-tab
1. Apply the configuration file.
1. Check the cluster status with clpstat command.
   ```
   [azureuser@centos01 ~]$ sudo clpstat
    ========================  CLUSTER STATUS  ===========================
     Cluster : cluster
     <server>
      *centos01 ........: Online
         lankhb1        : Normal           Kernel Mode LAN Heartbeat
         witnesshb1     : Normal           Witness Heartbeat
         httpnp1        : Normal           http resolution
       centos02 ........: Online
         lankhb1        : Normal           Kernel Mode LAN Heartbeat
         witnesshb1     : Normal           Witness Heartbeat
         httpnp1        : Normal           http resolution
     <group>
       failover ........: Online
       (snip)   
   ```
