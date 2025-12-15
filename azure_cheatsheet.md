# ☁️ Microsoft Azure Complete Cheat Sheet

> Your comprehensive guide to Azure services, commands, and best practices

[![Azure](https://img.shields.io/badge/Azure-0078D4?style=for-the-badge&logo=microsoft-azure&logoColor=white)](https://azure.microsoft.com/)

---

## 📋 Table of Contents
- [Core Services Overview](#core-services-overview)
- [Virtual Machines (Compute)](#virtual-machines-compute)
- [Storage Accounts](#storage-accounts)
- [Virtual Networks (Networking)](#virtual-networks-networking)
- [Azure AD (Identity)](#azure-ad-identity)
- [Azure SQL Database](#azure-sql-database)
- [Azure Functions (Serverless)](#azure-functions-serverless)
- [AKS (Kubernetes)](#aks-azure-kubernetes-service)
- [ARM Templates (IaC)](#arm-templates-infrastructure-as-code)
- [Azure CLI Commands](#azure-cli-quick-reference)

---

## 🎯 Core Services Overview

```
┌─────────────────────────────────────────────────────────────┐
│                     MICROSOFT AZURE                          │
├─────────────────────────────────────────────────────────────┤
│                                                               │
│  COMPUTE              STORAGE              NETWORKING        │
│  ┌──────────┐        ┌──────────┐        ┌──────────┐      │
│  │   VMs    │        │  Blob    │        │   VNet   │      │
│  │Functions │        │  Files   │        │   NSG    │      │
│  │   ACI    │        │  Disks   │        │   LB     │      │
│  │   AKS    │        │  Queue   │        │App Gateway│     │
│  └──────────┘        └──────────┘        └──────────┘      │
│                                                               │
│  DATABASE             SECURITY            MANAGEMENT         │
│  ┌──────────┐        ┌──────────┐        ┌──────────┐      │
│  │Azure SQL │        │Azure AD  │        │  Monitor │      │
│  │Cosmos DB │        │Key Vault │        │ Log Analytics│  │
│  │PostgreSQL│        │Security  │        │  Portal  │      │
│  └──────────┘        └──────────┘        └──────────┘      │
│                                                               │
└─────────────────────────────────────────────────────────────┘
```

### Quick Service Selection Guide

| Need | Use This Service |
|------|------------------|
| Virtual Machines | **Azure VMs** |
| Object Storage | **Blob Storage** |
| File Shares | **Azure Files** |
| Container Orchestration | **AKS** |
| Serverless Functions | **Azure Functions** |
| SQL Database | **Azure SQL Database** |
| NoSQL Database | **Cosmos DB** |
| Identity Management | **Azure AD** |
| Virtual Network | **VNet** |
| Load Balancing | **Load Balancer / App Gateway** |
| CDN | **Azure CDN** |
| DNS | **Azure DNS** |
| Monitoring | **Azure Monitor** |

---

## 💻 Virtual Machines (Compute)

### VM Series Quick Reference

| Series | Use Case | Example Size |
|--------|----------|--------------|
| **B-series** | Burstable, dev/test | B2s, B2ms |
| **D-series** | General purpose | D2s_v3, D4s_v3 |
| **E-series** | Memory optimized | E4s_v3, E8s_v3 |
| **F-series** | Compute optimized | F4s_v2, F8s_v2 |
| **N-series** | GPU instances | NC6, NV6 |

### Azure CLI - VM Commands

```bash
# Create resource group
az group create --name myResourceGroup --location eastus

# List VM sizes
az vm list-sizes --location eastus

# Create VM
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image Ubuntu2204 \
    --size Standard_B2s \
    --admin-username azureuser \
    --generate-ssh-keys

# List VMs
az vm list --output table

# Start VM
az vm start --resource-group myResourceGroup --name myVM

# Stop VM
az vm stop --resource-group myResourceGroup --name myVM

# Deallocate VM (stops billing)
az vm deallocate --resource-group myResourceGroup --name myVM

# Delete VM
az vm delete --resource-group myResourceGroup --name myVM --yes

# Get VM IP address
az vm list-ip-addresses --resource-group myResourceGroup --name myVM

# Resize VM
az vm resize --resource-group myResourceGroup --name myVM --size Standard_D2s_v3

# Run command on VM
az vm run-command invoke \
    --resource-group myResourceGroup \
    --name myVM \
    --command-id RunShellScript \
    --scripts "sudo apt-get update && sudo apt-get install -y nginx"
```

### VM Availability Options

| Option | Uptime SLA | Use Case |
|--------|-----------|----------|
| **Single VM** | 99.9% (Premium SSD) | Dev/Test |
| **Availability Set** | 99.95% | Multiple VMs, same datacenter |
| **Availability Zone** | 99.99% | Multiple VMs, different datacenters |
| **Virtual Machine Scale Set** | 99.95%+ | Auto-scaling workloads |

### 🎯 Interview Answers

**Q: What's the difference between Stop and Deallocate?**
> "Stop keeps the VM allocated (still billed for compute). Deallocate releases compute resources (only billed for storage). Always deallocate to save costs."

**Q: Availability Set vs Availability Zone?**
> "Availability Set protects from rack failures within a datacenter (99.95% SLA). Availability Zone protects from entire datacenter failure (99.99% SLA)."

---

## 🗄️ Storage Accounts

### Storage Account Types

| Type | Performance | Use Case |
|------|-------------|----------|
| **Standard (HDD)** | Standard | General purpose, low cost |
| **Premium (SSD)** | Premium | High IOPS, low latency |

### Storage Services

```
Storage Account
├── Blob Storage (Object storage like S3)
│   ├── Hot Tier (frequent access)
│   ├── Cool Tier (infrequent, 30 days min)
│   └── Archive Tier (rarely, 180 days min)
├── File Storage (SMB file shares)
├── Queue Storage (Message queue)
├── Table Storage (NoSQL key-value)
└── Disk Storage (VM disks)
```

### Blob Storage Tiers

| Tier | Access | Min Duration | Cost | Use Case |
|------|--------|--------------|------|----------|
| **Hot** | Frequent | None | 💰💰💰 | Active data |
| **Cool** | Infrequent | 30 days | 💰💰 | Backups, short-term |
| **Archive** | Rare | 180 days | 💰 | Long-term compliance |

### Azure CLI - Storage Commands

```bash
# Create storage account
az storage account create \
    --name mystorageaccount \
    --resource-group myResourceGroup \
    --location eastus \
    --sku Standard_LRS

# Get storage account keys
az storage account keys list \
    --resource-group myResourceGroup \
    --account-name mystorageaccount

# Create blob container
az storage container create \
    --name mycontainer \
    --account-name mystorageaccount

# Upload blob
az storage blob upload \
    --account-name mystorageaccount \
    --container-name mycontainer \
    --name myfile.txt \
    --file ./myfile.txt

# List blobs
az storage blob list \
    --account-name mystorageaccount \
    --container-name mycontainer \
    --output table

# Download blob
az storage blob download \
    --account-name mystorageaccount \
    --container-name mycontainer \
    --name myfile.txt \
    --file ./downloaded.txt

# Delete blob
az storage blob delete \
    --account-name mystorageaccount \
    --container-name mycontainer \
    --name myfile.txt

# Generate SAS token
az storage blob generate-sas \
    --account-name mystorageaccount \
    --container-name mycontainer \
    --name myfile.txt \
    --permissions r \
    --expiry 2025-12-31T23:59:59Z

# Create file share
az storage share create \
    --name myshare \
    --account-name mystorageaccount
```

### 🎯 Interview Answers

**Q: What's the difference between Blob Storage and File Storage?**
> "Blob Storage is object storage (REST API, unstructured data, like S3). Azure Files is SMB file shares (mount like network drive, structured hierarchical storage)."

**Q: When to use each blob tier?**
> "Hot for frequently accessed data, Cool for backups accessed occasionally (min 30 days), Archive for compliance/long-term retention (min 180 days, retrieval takes hours)."

---

## 🌐 Virtual Networks (Networking)

### VNet Architecture

```
┌──────────────────────────────────────────────────────────┐
│              Virtual Network (10.0.0.0/16)               │
│                                                            │
│  ┌─────────────────────┐    ┌─────────────────────┐     │
│  │  Subnet 1           │    │  Subnet 2           │     │
│  │  (10.0.1.0/24)      │    │  (10.0.2.0/24)      │     │
│  │                     │    │                     │     │
│  │  ┌──────┐ ┌──────┐ │    │  ┌──────┐ ┌──────┐ │     │
│  │  │ VM 1 │ │ VM 2 │ │    │  │ SQL  │ │ Redis│ │     │
│  │  │(web) │ │(app) │ │    │  │  DB  │ │      │ │     │
│  │  └──────┘ └──────┘ │    │  └──────┘ └──────┘ │     │
│  │                     │    │                     │     │
│  │  NSG (Firewall)     │    │  NSG (Firewall)     │     │
│  └─────────────────────┘    └─────────────────────┘     │
│                                                            │
│           Load Balancer / Application Gateway            │
└──────────────────────────────────────────────────────────┘
                          ↕
                    [Internet]
```

### Networking Components

| Component | Purpose |
|-----------|---------|
| **VNet** | Isolated network (like AWS VPC) |
| **Subnet** | Segment within VNet |
| **NSG** | Network Security Group (firewall rules) |
| **ASG** | Application Security Group (logical grouping) |
| **VNet Peering** | Connect two VNets |
| **VPN Gateway** | Site-to-site VPN |
| **ExpressRoute** | Dedicated private connection |
| **Load Balancer** | Layer 4 load balancing |
| **Application Gateway** | Layer 7 load balancing (HTTP/HTTPS) |
| **Azure DNS** | DNS hosting |

### Azure CLI - Networking Commands

```bash
# Create VNet
az network vnet create \
    --resource-group myResourceGroup \
    --name myVNet \
    --address-prefix 10.0.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 10.0.1.0/24

# Create NSG
az network nsg create \
    --resource-group myResourceGroup \
    --name myNSG

# Add NSG rule (allow SSH)
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNSG \
    --name AllowSSH \
    --priority 1000 \
    --source-address-prefixes '*' \
    --destination-port-ranges 22 \
    --access Allow \
    --protocol Tcp

# Create public IP
az network public-ip create \
    --resource-group myResourceGroup \
    --name myPublicIP \
    --sku Standard

# Create load balancer
az network lb create \
    --resource-group myResourceGroup \
    --name myLoadBalancer \
    --sku Standard \
    --public-ip-address myPublicIP

# List VNets
az network vnet list --output table

# VNet peering
az network vnet peering create \
    --resource-group myResourceGroup \
    --name myVNet-to-otherVNet \
    --vnet-name myVNet \
    --remote-vnet /subscriptions/.../otherVNet \
    --allow-vnet-access
```

### NSG Rules

```
Priority: 100-4096 (lower = higher priority)
Action: Allow or Deny
Source: IP address, CIDR, Service Tag, ASG
Destination: IP address, CIDR, Service Tag, ASG
Protocol: TCP, UDP, ICMP, Any
Port: Single port, range, or *
```

### 🎯 Interview Answers

**Q: What's the difference between NSG and ASG?**
> "NSG is a firewall with rules based on IP/port. ASG is a logical grouping of VMs (like 'web-servers') so NSG rules can reference groups instead of individual IPs."

**Q: Load Balancer vs Application Gateway?**
> "Load Balancer is Layer 4 (TCP/UDP), simple and fast. Application Gateway is Layer 7 (HTTP/HTTPS) with URL routing, SSL termination, WAF, more features but higher cost."

---

## 🔐 Azure AD (Identity)

### Azure AD Components

```
┌─────────────────────────────────────────────┐
│              Azure AD Tenant                 │
│                                              │
│  ┌────────┐  ┌────────┐  ┌────────┐        │
│  │ Users  │  │ Groups │  │  Apps  │        │
│  └────────┘  └────────┘  └────────┘        │
│       │           │            │            │
│       └───────────┴────────────┘            │
│                   │                         │
│         ┌─────────────────┐                 │
│         │  Roles & RBAC   │                 │
│         └─────────────────┘                 │
│                                              │
│  • Multi-Factor Auth (MFA)                  │
│  • Conditional Access                       │
│  • Single Sign-On (SSO)                     │
└─────────────────────────────────────────────┘
```

### Built-in Azure Roles

| Role | Permissions | Scope |
|------|-------------|-------|
| **Owner** | Full access, manage RBAC | All resources |
| **Contributor** | Create/manage resources, no RBAC | All resources |
| **Reader** | View only, no changes | All resources |
| **User Access Administrator** | Manage user access only | Access control |

### Azure CLI - Azure AD Commands

```bash
# List users
az ad user list --output table

# Create user
az ad user create \
    --display-name "John Doe" \
    --user-principal-name john@yourdomain.com \
    --password P@ssw0rd123

# List groups
az ad group list --output table

# Create group
az ad group create \
    --display-name "Developers" \
    --mail-nickname developers

# Add user to group
az ad group member add \
    --group Developers \
    --member-id <user-object-id>

# Assign role to user
az role assignment create \
    --assignee john@yourdomain.com \
    --role Contributor \
    --scope /subscriptions/<subscription-id>/resourceGroups/myResourceGroup

# List role assignments
az role assignment list --output table

# List service principals
az ad sp list --output table
```

### 🎯 Interview Answers

**Q: What's the difference between Azure AD and Active Directory?**
> "Azure AD is cloud-based identity service for modern apps (OAuth, SAML, SSO). Active Directory is on-premises directory service (Kerberos, LDAP, domain join). Can sync between them."

**Q: What is RBAC?**
> "Role-Based Access Control. Assign roles (Owner, Contributor, Reader) to users/groups at different scopes (subscription, resource group, resource) to grant least-privilege access."

---

## 🗃️ Azure SQL Database

### Service Tiers

| Tier | Use Case | Max Storage | Max vCores |
|------|----------|-------------|------------|
| **Basic** | Dev/test, small apps | 2 GB | - |
| **Standard** | Web apps, workgroups | 1 TB | - |
| **Premium** | Mission-critical | 4 TB | - |
| **General Purpose** | Most workloads | 4 TB | 80 |
| **Business Critical** | Low latency, HA | 4 TB | 80 |
| **Hyperscale** | Large databases | 100 TB | 80 |

### Azure SQL Features

- **Automatic Backups**: Point-in-time restore (7-35 days)
- **Geo-Replication**: Up to 4 read replicas in different regions
- **Auto-Tuning**: Automatic performance optimization
- **Threat Detection**: Security alerts
- **Elastic Pools**: Share resources among multiple databases
- **Always Encrypted**: Data encryption in use

### Azure CLI - SQL Commands

```bash
# Create SQL server
az sql server create \
    --resource-group myResourceGroup \
    --name myserver \
    --location eastus \
    --admin-user sqladmin \
    --admin-password P@ssw0rd123

# Create firewall rule
az sql server firewall-rule create \
    --resource-group myResourceGroup \
    --server myserver \
    --name AllowMyIP \
    --start-ip-address 1.2.3.4 \
    --end-ip-address 1.2.3.4

# Create database
az sql db create \
    --resource-group myResourceGroup \
    --server myserver \
    --name mydb \
    --service-objective S0

# List databases
az sql db list --resource-group myResourceGroup --server myserver --output table

# Scale database
az sql db update \
    --resource-group myResourceGroup \
    --server myserver \
    --name mydb \
    --service-objective S1

# Create geo-replica
az sql db replica create \
    --resource-group myResourceGroup \
    --server myserver \
    --name mydb \
    --partner-server myserver-replica \
    --partner-resource-group myResourceGroup

# Delete database
az sql db delete --resource-group myResourceGroup --server myserver --name mydb
```

### Connection String

```
Server=tcp:myserver.database.windows.net,1433;
Database=mydb;
User ID=sqladmin;
Password=P@ssw0rd123;
Encrypt=true;
Connection Timeout=30;
```

### 🎯 Interview Answers

**Q: Azure SQL Database vs SQL Server on VM?**
> "Azure SQL Database is PaaS (managed service, automatic backups/patching, elastic scaling). SQL Server on VM is IaaS (full control, custom config, you manage everything). Use Database for ease, VM for specific requirements."

**Q: What is an Elastic Pool?**
> "Shared resource pool for multiple databases with unpredictable usage patterns. Cost-effective when databases have varying peak times - they share vCores and storage."

---

## ⚡ Azure Functions (Serverless)

### Hosting Plans

| Plan | Scaling | Timeout | Use Case |
|------|---------|---------|----------|
| **Consumption** | Auto (0-200 instances) | 5 min (default) | Event-driven, sporadic |
| **Premium** | Pre-warmed, auto-scale | 30 min (default) | No cold starts, VNet |
| **Dedicated (App Service)** | Manual/auto-scale | Unlimited | Predictable, existing ASP |

### Supported Languages

- C# / .NET 8, 6
- JavaScript / TypeScript (Node.js)
- Python 3.11, 3.10, 3.9
- Java 17, 11, 8
- PowerShell 7.2

### Sample Function (JavaScript)

```javascript
module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    const name = (req.query.name || (req.body && req.body.name));
    const responseMessage = name
        ? "Hello, " + name + "!"
        : "Please pass a name on the query string or in the request body";

    context.res = {
        status: 200,
        body: responseMessage
    };
}
```

### Azure CLI - Functions Commands

```bash
# Create function app
az functionapp create \
    --resource-group myResourceGroup \
    --name myfunctionapp \
    --storage-account mystorageaccount \
    --consumption-plan-location eastus \
    --runtime node \
    --runtime-version 18 \
    --functions-version 4

# List function apps
az functionapp list --output table

# Deploy function app
func azure functionapp publish myfunctionapp

# View function logs
az webapp log tail --name myfunctionapp --resource-group myResourceGroup

# Get function app settings
az functionapp config appsettings list \
    --name myfunctionapp \
    --resource-group myResourceGroup

# Set function app setting
az functionapp config appsettings set \
    --name myfunctionapp \
    --resource-group myResourceGroup \
    --settings "MY_KEY=my_value"

# Delete function app
az functionapp delete --name myfunctionapp --resource-group myResourceGroup
```

### Common Triggers

- **HTTP Trigger**: REST API endpoints
- **Timer Trigger**: Scheduled tasks (cron)
- **Blob Trigger**: When blob is created/updated
- **Queue Trigger**: Process queue messages
- **Event Grid**: React to Azure events
- **Service Bus**: Message processing
- **Cosmos DB**: React to database changes

### 🎯 Interview Answers

**Q: When to use Consumption vs Premium plan?**
> "Consumption for event-driven, sporadic workloads (cheapest, pay per execution). Premium for consistent workloads needing no cold starts, VNet integration, or longer execution times."

**Q: Azure Functions vs Logic Apps?**
> "Functions are code-based, developer-focused, fine-grained control. Logic Apps are low-code, workflow-focused, many built-in connectors. Use Functions for custom logic, Logic Apps for integration workflows."

---

## ☸️ AKS (Azure Kubernetes Service)

### AKS Architecture

```
┌────────────────────────────────────────────────────────┐
│                    AKS Cluster                          │
├────────────────────────────────────────────────────────┤
│                                                          │
│  ┌──────────────────────┐         ┌─────────────────┐ │
│  │   Control Plane      │         │   Node Pools    │ │
│  │   (Managed by Azure) │◄───────►│   (VM Scale Set)│ │
│  │                      │         │                 │ │
│  │  • API Server        │         │  ┌───────────┐  │ │
│  │  • etcd              │         │  │ Node 1    │  │ │
│  │  • Scheduler         │         │  │ Pods...   │  │ │
│  │  • Controller        │         │  └───────────┘  │ │
│  └──────────────────────┘         │                 │ │
│                                    │  ┌───────────┐  │ │
│  Control Plane: FREE               │  │ Node 2    │  │ │
│                                    │  │ Pods...   │  │ │
│                                    │  └───────────┘  │ │
│                                    └─────────────────┘ │
│                                                          │
│  Integrated with: Azure LB, Azure Disks, Azure AD      │
└────────────────────────────────────────────────────────┘
```

### Azure CLI - AKS Commands

```bash
# Create AKS cluster
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 3 \
    --node-vm-size Standard_D2s_v3 \
    --enable-addons monitoring \
    --generate-ssh-keys

# Get credentials
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster

# List clusters
az aks list --output table

# Scale cluster
az aks scale \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 5

# Upgrade cluster
az aks upgrade \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --kubernetes-version 1.28.0

# Add node pool
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name gpunodepool \
    --node-count 2 \
    --node-vm-size Standard_NC6

# Delete cluster
az aks delete --resource-group myResourceGroup --name myAKSCluster --yes

# Browse dashboard
az aks browse --resource-group myResourceGroup --name myAKSCluster
```

### AKS Features

- **Free Control Plane**: No cost for Kubernetes masters
- **Azure AD Integration**: RBAC with Azure AD
- **Azure Monitor**: Container insights
- **Virtual Nodes**: Serverless pods with Azure Container Instances
- **Network Policies**: Calico or Azure
- **Cluster Autoscaler**: Auto-scale nodes
- **Azure Policy**: Governance and compliance

### 🎯 Interview Answers

**Q: What makes AKS different from other managed Kubernetes?**
> "AKS has free control plane (best free tier), deep Azure integration (Azure AD, Monitor, Policy), virtual nodes for serverless pods, and Azure support for the entire stack."

**Q: Kubenet vs Azure CNI?**
> "Kubenet is simpler (nodes get VNet IPs, pods use internal range). Azure CNI gives pods real VNet IPs (better for VNet integration, but uses more IPs)."

---

## 📦 ARM Templates (Infrastructure as Code)

### Sample ARM Template

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "defaultValue": "myVM",
      "metadata": {
        "description": "Name of the virtual machine"
      }
    },
    "adminUsername": {
      "type": "string",
      "metadata": {
        "description": "Admin username"
      }
    },
    "adminPassword": {
      "type": "securestring",
      "metadata": {
        "description": "Admin password"
      }
    }
  },
  "variables": {
    "nicName": "[concat(parameters('vmName'), '-nic')]",
    "vnetName": "[concat(parameters('vmName'), '-vnet')]"
  },
  "resources": [
    {
      "type": "Microsoft.Network/virtualNetworks",
      "apiVersion": "2021-02-01",
      "name": "[variables('vnetName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "addressSpace": {
          "addressPrefixes": ["10.0.0.0/16"]
        },
        "subnets": [
          {
            "name": "default",
            "properties": {
              "addressPrefix": "10.0.0.0/24"
            }
          }
        ]
      }
    },
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2021-03-01",
      "name": "[parameters('vmName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "hardwareProfile": {
          "vmSize": "Standard_B2s"
        },
        "osProfile": {
          "computerName": "[parameters('vmName')]",
          "adminUsername": "[parameters('adminUsername')]",
          "adminPassword": "[parameters('adminPassword')]"
        },
        "storageProfile": {
          "imageReference": {
            "publisher": "Canonical",
            "offer": "0001-com-ubuntu-server-jammy",
            "sku": "22_04-lts",
            "version": "latest"
          }
        }
      }
    }
  ],
  "outputs": {
    "vmId": {
      "type": "string",
      "value": "[resourceId('Microsoft.Compute/virtualMachines', parameters('vmName'))]"
    }
  }
}
```

### Azure CLI - ARM Template Commands

```bash
# Deploy template
az deployment group create \
    --resource-group myResourceGroup \
    --template-file template.json \
    --parameters vmName=myVM adminUsername=azureuser adminPassword=P@ssw0rd123

# Validate template
az deployment group validate \
    --resource-group myResourceGroup \
    --template-file template.json

# What-if (preview changes)
az deployment group what-if \
    --resource-group myResourceGroup \
    --template-file template.json

# Export resource group template
az group export --name myResourceGroup --output json > template.json
```

### 🎯 Interview Answers

**Q: ARM Templates vs Terraform?**
> "ARM Templates are Azure-native (JSON, deep integration). Terraform is multi-cloud (HCL, better for multi-cloud). Bicep is newer Azure alternative (cleaner syntax than ARM JSON)."

---

## 🔧 Azure CLI Quick Reference

### Installation & Configuration

```bash
# Install Azure CLI
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash

# Login
az login

# Set subscription
az account set --subscription "My Subscription"

# List subscriptions
az account list --output table

# Show current account
az account show

# Set default location
az configure --defaults location=eastus

# Set default resource group
az configure --defaults group=myResourceGroup
```

### Common Commands

```bash
# List resource groups
az group list --output table

# Create resource group
az group create --name myResourceGroup --location eastus

# Delete resource group
az group delete --name myResourceGroup --yes --no-wait

# List resources
az resource list --output table

# Get resource ID
az resource show --name myVM --resource-group myResourceGroup --resource-type Microsoft.Compute/virtualMachines --query id

# Tag resource
az resource tag --tags Environment=Dev Project=MyApp --resource-group myResourceGroup --name myVM --resource-type Microsoft.Compute/virtualMachines

# Output formats
az vm list --output json
az vm list --output table
az vm list --output yaml
az vm list --output tsv

# Query with JMESPath
az vm list --query "[?powerState=='VM running'].{Name:name, RG:resource