## Generating Your Azure Environment

This is the step by step method of setting up the components for a live ELK deployment on Azure.

This document contains the following details:  
- Description of the Topology  
- Setup of the Different Components within an Azure Environment  
  - Resource Group  
  - Network Security Group  
  - Virtual Machine (JumpBox Provisioner)  
  - Virtual Machines (Web Machines)  
  - Virtual Machine (ELK Machine)  
  - Load Balancer  
  - Access Policies created within the Network Security Groups  
  - Containers setup through Docker

### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

A single point of entry to configure the network is created through the JumpBox Provisioner. From there is access to the load balanced web machines, as many as desired. There is also access to the ELK Server virtual machine. Each subnet will have it's own assoscated Security Group which all lies within one Resource Group. 

The configuration details of each machine may be found below.

#### Resource Group

A resource group is a grouping of all resources used for a project, from networks, firewalls, virtual computers and more.  
  - Select Resource Group and Click Add  
  - Select your region and give the group a name  
  - Review and Create  
  ![](Diagrams/Create_Resource_Group.PNG)
  
#### Virtual Network

A virtual Network is a collection of Virtual Machines that communicate with each other. Two will be created for this projected, one for the first subnet containing the Web Machines and one for the ELK subnet. These two networks will have to be connected to allow traffic to go back and forth the Networks by generating a Peer Connection.  

##### Web based Virtual Network  
  - Select Virtual Network and Click Create Virtual Network  
  - Select the Resource Group that was created  
  - Enter a name that will define the Network  
  - Make sure that it is in the same region  
  - Leave all settings as is in the other tabs  
  - Review and Create  
  ![](Diagrams/Create_Virtual_Network.PNG)    
  
##### ELK based Virtual Network  
  - Select Virtual Network and Click Create Virtual Network  
  - Select the Resource Group that was created  
  - Enter a name that will define the ELK Network  
  - Choose a different region  
  - Under IP Addresses, notice that a new subnet has been created  
  - Leave all settings as is in the other tabs  
  - Review and Create  
  ![](Diagrams/Create_Virtual_Network_ELK.PNG)    
  
##### Peer Connection  
  - Go to the ELK Virtual Network that was just created and select Peerings  
  - Click Add  
  - Enter a name that will define the connection from ELK to Red  
  - Under Virtual Network, select the original Web network  
  - Enter a name that will define the connection from Red to ELK  
  - Leave all other settings as defaults  
  ![](Diagrams/Create_Virtual_Network_ELK_Peering.PNG)  
  
#### Virtual Machines

A Virtual Machine is just like a physical computer but has the flexibility to have the power and storage that is desired for each individual machine. For this project we will generate our point of entry machine, called our JumpBox Provisioner. We will also create several load balanced Web Machines that will be connecting to the Internet and then a final ELK Server machine that will be running the ELK Stack monitoring. All these machines will be connected by SSH keys going through port 22.  

#### Generating SSH Key
  - Open terminal on desired machine and run ssh-keygen, this will generate a SSH key into the default directory of ~/.ssh/id_rsa  
  - Do not enter a password, press enter twice to leave a blank password  
  - To access the key run cat ~/.ssh/id_rsa.pub  

##### JumpBox Provisioner  

  - Select Virtual Machines, click add, click virtual machine  
  - Select the Resource Group that was just created  
  - Enter a name that will define the Virtual Machine  
  - Make sure that it is in the region chosen for Web Virtual Network  
  - Select the size of the virtual machine that is desired (for this project B1s was chosen)  
  - Select SSH public key  
  - Enter in a username that will be used to SSH into the JumpBox from the host machine
  - For SSH public key source, select Use existing public key  
  - For SSH public key, paste in the key that was generated on the host machine  
  - Under the Networking tab make sure Public IP has something in the field  
  - Under NIC network security group, select Advanced, then for Configure network security group select the security group that we created.  
  ![](Diagrams/Create_Virtual_Machine_JumpBox1.PNG)  
  ![](Diagrams/Create_Virtual_Machine_JumpBox2.PNG)  
  ![](Diagrams/Create_Virtual_Machine_JumpBox3.PNG)  
  
##### Web Virtual Machines  

  - Select Virtual Machines, click add, click virtual machine  
  - Select the Resource Group that was created  
  - Enter a name that will define the Virtual Machine  
  - For Availability Options, select Availability Set  
  - For Availability Set, Create New (Any additional web machines will just select the availability set created)  
![](Diagrams/Create_Virtual_Machine_Web_Availability)  	
  - Make sure that it is in the region chosen for Web Virtual Network  
  - Select the size of the virtual machine that is desired (for these machines B1ms was chosen)  
  - Select SSH public key  
  - Enter in a username that will be used to SSH into the virtual machines from the JumpBox  
  - For SSH public key source, select Use existing public key  
  - Copy the SSH key generated within the Ansible container of the JumpBox  
  - Under the Networking tab, for Public IP select None (these machines will eventually get the Load Balancer IP address)  
  - Under NIC network security group, select Advanced, then for Configure network security group select the security group that we created.  
![](Diagrams/Create_Virtual_Machine_Web1.PNG)  
![](Diagrams/Create_Virtual_Machine_Web2.PNG)  
![](Diagrams/Create_Virtual_Machine_Web3.PNG)  
