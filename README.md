# Cloud-Networking-Private-Endpoint-Lab
Azure networking lab showcasing a VNet, NSG rules, and a Private Endpoint to securely access a Storage Account without public exposure. Demonstrates zero‑trust design and private PaaS access.
Azure Private Endpoint Lab
This project demonstrates how to securely expose an Azure Storage Account using a Private Endpoint, ensuring all traffic stays on the Azure backbone and no public network access is allowed. The lab walks through building a minimal, production‑aligned network foundation and validating private‑only connectivity.

Project Overview
This lab simulates a real‑world scenario where an organization needs to access Azure Storage without exposing it to the public internet. Using Private Link, a private IP is created inside a virtual network and mapped directly to the Storage Account’s blob service.
The result:
• 	No public access
• 	No inbound internet exposure
• 	All traffic flows privately through Azure’s backbone
• 	DNS resolves the Storage Account to a 10.x.x.x address inside the VNet
This is a core pattern in Zero Trust architectures and modern cloud security.
               +-----------------------------+
               |     Azure Storage Account   |
               |   stprivatelabXXXX (Blob)   |
               |   Public Access: Disabled   |
               +--------------+--------------+
                              |
                              | Private Link
                              v
                 +---------------------------+
                 |   Private Endpoint (PEP)  |
                 |   IP: 10.0.1.x            |
                 +-------------+-------------+
                               |
                               v
               +-----------------------------+
               |   VNet: vnet-azure-vm-...   |
               |   Subnet: subnet-servers    |
               |   NSG: nsg-private-endpoint |
               +-----------------------------+
                               |
                               v
                 +---------------------------+
                 | Private DNS Zone          |
                 | privatelink.blob.core...  |
                 | A‑Record → 10.0.1.x       |
                 +---------------------------+
                 Step‑by‑Step Deployment Summary
1. 	Resource Group
Used existing resource group: rg-azure-vm-nsg-bastion
2. 	Virtual Network
Used existing VNet: vnet-azure-vm-nsg-bastion2
Subnet used: subnet-servers
3. 	Network Security Group
Created and associated: nsg-private-endpoint-lab → subnet-servers
4. 	Storage Account
Created with:
• 	Region: East US
• 	Redundancy: LRS
• 	Public network access: Disabled
• 	No public endpoints allowed
5. 	Private Endpoint
Created:
• 	Name: pep-storage-private
• 	Target: Storage Account (blob)
• 	Subnet: subnet-servers
• 	Private DNS integration: Enabled
6. 	Private DNS Zone
Azure automatically created: privatelink.blob.core.windows.net
A‑record added: stprivatelabXXXX → 10.0.1.x
7. 	Validation
• 	Storage Account unreachable publicly
• 	Private endpoint connection = Approved
• 	DNS resolves to private IP
• 	Public access = Disabled
What I Learned
1. 	Private Endpoints enforce true zero‑trust
A Private Endpoint creates a private IP inside a VNet that maps directly to a PaaS resource. This eliminates public exposure and forces all traffic through Azure’s backbone.
2. 	Public network access can be fully disabled
Disabling public access on the Storage Account ensures it cannot be reached from the internet, even with the correct URL.
3. 	DNS is critical for private connectivity
Azure automatically created a Private DNS Zone and registered an A‑record pointing to the private endpoint’s IP. This allows VMs inside the VNet to resolve the Storage Account privately.
4. 	NSGs still matter
Even with private endpoints, NSGs enforce subnet‑level security boundaries and remain part of the Zero Trust model.
5. 	Clean resource organization
All resources were deployed into a single resource group, making the environment easy to manage, audit, and delete.

Resources Used
• 	Azure Portal
• 	Azure Private Link documentation
• 	Azure Storage Account networking documentation
• 	Azure Private DNS Zones
