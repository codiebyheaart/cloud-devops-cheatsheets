# ☁️ Google Cloud Platform (GCP) Complete Cheat Sheet

> Your comprehensive guide to GCP services, commands, and best practices

[![GCP](https://img.shields.io/badge/GCP-4285F4?style=for-the-badge&logo=google-cloud&logoColor=white)](https://cloud.google.com/)

---

## 📋 Table of Contents
- [Core Services Overview](#core-services-overview)
- [Compute Engine (VMs)](#compute-engine-virtual-machines)
- [Cloud Storage](#cloud-storage)
- [VPC Networking](#vpc-networking)
- [IAM (Identity)](#iam-identity--access-management)
- [Cloud SQL](#cloud-sql)
- [Cloud Functions (Serverless)](#cloud-functions-serverless)
- [GKE (Kubernetes)](#gke-google-kubernetes-engine)
- [Deployment Manager (IaC)](#deployment-manager-infrastructure-as-code)
- [gcloud CLI Commands](#gcloud-cli-quick-reference)

---

## 🎯 Core Services Overview

```
┌─────────────────────────────────────────────────────────────┐
│                  GOOGLE CLOUD PLATFORM                       │
├─────────────────────────────────────────────────────────────┤
│                                                               │
│  COMPUTE              STORAGE              NETWORKING        │
│  ┌──────────┐        ┌──────────┐        ┌──────────┐      │
│  │Compute   │        │  Cloud   │        │   VPC    │      │
│  │Engine    │        │ Storage  │        │Cloud Load│      │
│  │Cloud Run │        │Persistent│        │Balancing │      │
│  │   GKE    │        │  Disk    │        │Cloud CDN │      │
│  └──────────┘        └──────────┘        └──────────┘      │
│                                                               │
│  DATABASE             SECURITY            MANAGEMENT         │
│  ┌──────────┐        ┌──────────┐        ┌──────────┐      │
│  │Cloud SQL │        │   IAM    │        │ Cloud    │      │
│  │Firestore │        │Secret Mgr│        │Monitoring│      │
│  │BigQuery  │        │Cloud KMS │        │Cloud     │      │
│  │Spanner   │        │          │        │Logging   │      │
│  └──────────┘        └──────────┘        └──────────┘      │
│                                                               │
└─────────────────────────────────────────────────────────────┘
```

### Quick Service Selection Guide

| Need | Use This Service |
|------|------------------|
| Virtual Machines | **Compute Engine** |
| Containers (Serverless) | **Cloud Run** |
| Container Orchestration | **GKE** |
| Object Storage | **Cloud Storage** |
| Block Storage | **Persistent Disk** |
| Serverless Functions | **Cloud Functions** |
| SQL Database | **Cloud SQL** |
| NoSQL Document DB | **Firestore** |
| NoSQL Wide-Column | **Bigtable** |
| Data Warehouse | **BigQuery** |
| Virtual Network | **VPC** |
| Load Balancing | **Cloud Load Balancing** |
| Identity Management | **IAM** |
| Monitoring | **Cloud Monitoring** |

---

## 💻 Compute Engine (Virtual Machines)

### Machine Types Quick Reference

| Family | Type | Use Case | vCPUs | Memory |
|--------|------|----------|-------|--------|
| **General Purpose** | n1-standard-1 | Balanced workloads | 1 | 3.75 GB |
| | e2-medium | Cost-optimized | 2 | 4 GB |
| | n2-standard-4 | Balanced | 4 | 16 GB |
| **Compute Optimized** | c2-standard-4 | High CPU | 4 | 16 GB |
| **Memory Optimized** | m2-ultramem-208 | Large memory | 208 | 5,888 GB |
| **Accelerator** | a2-highgpu-1g | ML/AI workloads | 12 | 85 GB |

### Machine Families

- **E2**: Cost-optimized, burstable (like AWS t3)
- **N1**: First-generation, general purpose
- **N2**: Second-generation, better performance
- **N2D**: AMD-based, cost-effective
- **C2**: Compute-optimized, highest performance per core
- **M2**: Memory-optimized, large datasets
- **A2**: GPU instances for ML/AI

### gcloud Commands - Compute Engine

```bash
# List all instances
gcloud compute instances list

# Create instance
gcloud compute instances create my-instance \
    --zone=us-central1-a \
    --machine-type=e2-medium \
    --image-family=ubuntu-2204-lts \
    --image-project=ubuntu-os-cloud \
    --boot-disk-size=10GB \
    --tags=http-server,https-server

# Start instance
gcloud compute instances start my-instance --zone=us-central1-a

# Stop instance
gcloud compute instances stop my-instance --zone=us-central1-a

# Delete instance
gcloud compute instances delete my-instance --zone=us-central1-a

# SSH into instance
gcloud compute ssh my-instance --zone=us-central1-a

# Get external IP
gcloud compute instances describe my-instance \
    --zone=us-central1-a \
    --format='get(networkInterfaces[0].accessConfigs[0].natIP)'

# Add startup script
gcloud compute instances create my-instance \
    --metadata=startup-script='#!/bin/bash
    apt-get update
    apt-get install -y nginx'

# Create from custom image
gcloud compute instances create my-instance \
    --image=my-custom-image \
    --image-project=my-project

# Create with preemptible (spot)
gcloud compute instances create my-instance \
    --preemptible \
    --zone=us-central1-a

# Resize instance (change machine type)
gcloud compute instances set-machine-type my-instance \
    --machine-type=n2-standard-4 \
    --zone=us-central1-a
```

### Instance Templates & Managed Instance Groups

```bash
# Create instance template
gcloud compute instance-templates create my-template \
    --machine-type=e2-medium \
    --image-family=ubuntu-2204-lts \
    --image-project=ubuntu-os-cloud

# Create managed instance group (auto-scaling)
gcloud compute instance-groups managed create my-mig \
    --template=my-template \
    --size=3 \
    --zone=us-central1-a

# Set autoscaling
gcloud compute instance-groups managed set-autoscaling my-mig \
    --max-num-replicas=10 \
    --min-num-replicas=2 \
    --target-cpu-utilization=0.6 \
    --zone=us-central1-a

# Update instance group
gcloud compute instance-groups managed rolling-action start-update my-mig \
    --version=template=my-new-template \
    --zone=us-central1-a
```

### 🎯 Interview Answers

**Q: What are preemptible instances?**
> "Preemptible VMs are short-lived (max 24 hours), up to 80% cheaper than regular instances. Good for batch processing, fault-tolerant workloads. Similar to AWS Spot Instances."

**Q: What's the difference between machine families?**
> "E2 is cost-optimized with shared CPU, N2 is balanced general purpose, C2 is compute-optimized with highest per-core performance, M2 is memory-optimized for large datasets."

---

## 🗄️ Cloud Storage

### Storage Classes

| Class | Use Case | Min Duration | Retrieval | Cost |
|-------|----------|--------------|-----------|------|
| **Standard** | Hot data, frequent access | None | Instant | 💰💰💰 |
| **Nearline** | Backup, accessed monthly | 30 days | Instant | 💰💰 |
| **Coldline** | Disaster recovery, quarterly | 90 days | Instant | 💰 |
| **Archive** | Long-term, yearly access | 365 days | Instant | ¢ |

### Storage Features

- **11 9's Durability**: 99.999999999%
- **Multi-Regional**: Geo-redundant across regions
- **Regional**: Single region, lower cost
- **Dual-Regional**: Two specific regions
- **Object Lifecycle**: Auto-transition/delete objects
- **Versioning**: Keep object versions
- **Signed URLs**: Temporary access
- **Uniform vs Fine-grained ACL**: Bucket-level vs object-level

### gsutil Commands - Cloud Storage

```bash
# List buckets
gsutil ls

# Create bucket
gsutil mb gs://my-unique-bucket-name

# List objects in bucket
gsutil ls gs://my-bucket/

# Upload file
gsutil cp myfile.txt gs://my-bucket/

# Upload folder recursively
gsutil cp -r my-folder/ gs://my-bucket/

# Download file
gsutil cp gs://my-bucket/myfile.txt ./

# Download entire bucket
gsutil cp -r gs://my-bucket ./local-folder

# Sync local to bucket
gsutil rsync -r ./local-folder gs://my-bucket/folder

# Make object public
gsutil acl ch -u AllUsers:R gs://my-bucket/myfile.txt

# Delete object
gsutil rm gs://my-bucket/myfile.txt

# Delete bucket (must be empty)
gsutil rb gs://my-bucket

# Set storage class
gsutil defstorageclass set NEARLINE gs://my-bucket

# Enable versioning
gsutil versioning set on gs://my-bucket

# Set lifecycle rule
gsutil lifecycle set lifecycle.json gs://my-bucket
```

### Lifecycle Policy Example (lifecycle.json)

```json
{
  "lifecycle": {
    "rule": [
      {
        "action": {
          "type": "SetStorageClass",
          "storageClass": "NEARLINE"
        },
        "condition": {
          "age": 30
        }
      },
      {
        "action": {
          "type": "Delete"
        },
        "condition": {
          "age": 365
        }
      }
    ]
  }
}
```

### gcloud Commands - Cloud Storage

```bash
# Create bucket
gcloud storage buckets create gs://my-bucket --location=us-central1

# List buckets
gcloud storage buckets list

# Upload file
gcloud storage cp myfile.txt gs://my-bucket/

# Set bucket policy (make public)
gcloud storage buckets add-iam-policy-binding gs://my-bucket \
    --member=allUsers \
    --role=roles/storage.objectViewer

# Enable uniform bucket-level access
gcloud storage buckets update gs://my-bucket \
    --uniform-bucket-level-access
```

### 🎯 Interview Answers

**Q: What's the difference between storage classes?**
> "Standard for hot data (no minimum duration), Nearline for monthly access (30-day min), Coldline for quarterly (90-day min), Archive for yearly (365-day min). All have instant retrieval but different pricing."

**Q: Multi-Regional vs Regional vs Dual-Regional?**
> "Multi-Regional replicates across multiple regions (highest availability, most expensive). Regional is single region (lower cost). Dual-Regional is two specific regions (balance of availability and cost)."

---

## 🌐 VPC Networking

### VPC Architecture

```
┌──────────────────────────────────────────────────────────┐
│              VPC Network (Global Resource)                │
│                                                            │
│  Region: us-central1          Region: europe-west1       │
│  ┌─────────────────────┐    ┌─────────────────────┐     │
│  │  Subnet              │    │  Subnet             │     │
│  │  (10.128.0.0/20)     │    │  (10.132.0.0/20)    │     │
│  │                      │    │                     │     │
│  │  ┌──────┐ ┌──────┐  │    │  ┌──────┐ ┌──────┐ │     │
│  │  │ VM 1 │ │ VM 2 │  │    │  │ VM 3 │ │ VM 4 │ │     │
│  │  └──────┘ └──────┘  │    │  └──────┘ └──────┘ │     │
│  │                      │    │                     │     │
│  │  Firewall Rules      │    │  Firewall Rules     │     │
│  └─────────────────────┘    └─────────────────────┘     │
│                                                            │
│           Cloud Load Balancing (Global)                   │
└──────────────────────────────────────────────────────────┘
                          ↕
                    [Internet]
```

### VPC Key Concepts

| Component | Description |
|-----------|-------------|
| **VPC** | Global resource, spans all regions |
| **Subnet** | Regional resource, IP range per region |
| **Firewall Rules** | Allow/deny traffic, stateful |
| **Routes** | Define traffic paths |
| **VPC Peering** | Connect two VPCs privately |
| **Cloud VPN** | IPsec VPN to on-premises |
| **Cloud Interconnect** | Dedicated private connection |
| **Cloud NAT** | Outbound internet for private instances |
| **Cloud Load Balancing** | Global load distribution |

### gcloud Commands - VPC

```bash
# Create VPC network
gcloud compute networks create my-vpc \
    --subnet-mode=custom

# Create subnet
gcloud compute networks subnets create my-subnet \
    --network=my-vpc \
    --region=us-central1 \
    --range=10.128.0.0/20

# List networks
gcloud compute networks list

# List subnets
gcloud compute networks subnets list

# Create firewall rule (allow SSH)
gcloud compute firewall-rules create allow-ssh \
    --network=my-vpc \
    --allow=tcp:22 \
    --source-ranges=0.0.0.0/0

# Create firewall rule (allow HTTP/HTTPS)
gcloud compute firewall-rules create allow-http-https \
    --network=my-vpc \
    --allow=tcp:80,tcp:443 \
    --source-ranges=0.0.0.0/0 \
    --target-tags=web-server

# List firewall rules
gcloud compute firewall-rules list

# Delete firewall rule
gcloud compute firewall-rules delete allow-ssh

# Create static IP
gcloud compute addresses create my-static-ip \
    --region=us-central1

# List static IPs
gcloud compute addresses list

# VPC Peering
gcloud compute networks peerings create peer-to-other \
    --network=my-vpc \
    --peer-network=other-vpc \
    --peer-project=other-project
```

### Firewall Rules Priority

```
Priority: 0-65535 (lower number = higher priority)
Default: 1000
Action: allow or deny
Direction: ingress (inbound) or egress (outbound)
Source/Destination: IP ranges, tags, service accounts
```

### 🎯 Interview Answers

**Q: What makes GCP VPC different from AWS VPC?**
> "GCP VPC is global (subnets span regions), AWS VPC is regional. GCP has global load balancing built-in. GCP firewall rules are stateful at VPC level, AWS uses Security Groups and NACLs."

**Q: How do firewall rules work?**
> "Firewall rules are stateful (return traffic auto-allowed), applied at VPC level, evaluated by priority (lower number first), default deny all ingress and allow all egress. Can target by tags or service accounts."

---

## 🔐 IAM (Identity & Access Management)

### IAM Components

```
┌─────────────────────────────────────────────┐
│              GCP Organization                │
│                                              │
│  ┌────────┐  ┌────────┐  ┌────────┐        │
│  │ Users  │  │ Groups │  │Service │        │
│  │        │  │        │  │Accounts│        │
│  └────────┘  └────────┘  └────────┘        │
│       │           │            │            │
│       └───────────┴────────────┘            │
│                   │                         │
│              ┌────────┐                     │
│              │ Roles  │                     │
│              │ (IAM)  │                     │
│              └────────┘                     │
│          Who → Role → Resource              │
└─────────────────────────────────────────────┘
```

### IAM Hierarchy

```
Organization
    └── Folder (optional)
        └── Project
            └── Resource (VM, Bucket, etc.)

Inheritance: Permissions flow down the hierarchy
```

### Common Predefined Roles

| Role | Permissions | Use Case |
|------|-------------|----------|
| **Owner** | Full access + manage IAM | Project admin |
| **Editor** | Modify resources, no IAM | Developers |
| **Viewer** | Read-only access | Auditors, monitoring |
| **Compute Admin** | Full access to Compute Engine | VM management |
| **Storage Admin** | Full access to Cloud Storage | Storage management |
| **Network Admin** | Full access to networking | Network engineers |

### gcloud Commands - IAM

```bash
# List IAM policy for project
gcloud projects get-iam-policy PROJECT_ID

# Add IAM binding (grant role to user)
gcloud projects add-iam-policy-binding PROJECT_ID \
    --member=user:john@example.com \
    --role=roles/editor

# Remove IAM binding
gcloud projects remove-iam-policy-binding PROJECT_ID \
    --member=user:john@example.com \
    --role=roles/editor

# Grant role to service account
gcloud projects add-iam-policy-binding PROJECT_ID \
    --member=serviceAccount:my-sa@PROJECT_ID.iam.gserviceaccount.com \
    --role=roles/storage.admin

# Create service account
gcloud iam service-accounts create my-service-account \
    --display-name="My Service Account"

# List service accounts
gcloud iam service-accounts list

# Create service account key
gcloud iam service-accounts keys create key.json \
    --iam-account=my-sa@PROJECT_ID.iam.gserviceaccount.com

# Grant role to specific resource
gcloud storage buckets add-iam-policy-binding gs://my-bucket \
    --member=user:john@example.com \
    --role=roles/storage.objectViewer

# List roles
gcloud iam roles list

# Describe role
gcloud iam roles describe roles/compute.admin
```

### Service Account Best Practices

✅ **DO:**
- Use service accounts for applications
- Grant least privilege
- Rotate keys regularly
- Use short-lived tokens when possible
- Enable audit logging

❌ **DON'T:**
- Download service account keys (use Workload Identity)
- Share service account keys
- Grant Owner role to service accounts
- Use default service accounts in production

### 🎯 Interview Answers

**Q: What's the difference between a user account and service account?**
> "User accounts are for people (Google accounts). Service accounts are for applications/VMs with no passwords, use key files or Workload Identity for authentication."

**Q: Explain the IAM resource hierarchy**
> "Organization > Folder > Project > Resource. Permissions are inherited down the tree. Grant at highest appropriate level to reduce management overhead. Project is the billing boundary."

---

## 🗃️ Cloud SQL

### Supported Database Engines

| Engine | Max Storage | HA Option | Use Case |
|--------|-------------|-----------|----------|
| **MySQL** | 30 TB | Regional HA | Web apps, general purpose |
| **PostgreSQL** | 30 TB | Regional HA | Advanced features, GIS |
| **SQL Server** | 30 TB | Regional HA | Windows/.NET apps |

### Cloud SQL Features

- **Automatic Backups**: Point-in-time recovery
- **High Availability**: Failover to standby
- **Read Replicas**: Scale reads (cross-region)
- **Automatic Storage Increase**: Grows as needed
- **Encryption**: At rest and in transit
- **Private IP**: VPC connectivity
- **Cloud SQL Proxy**: Secure connections
- **Maintenance Windows**: Scheduled updates

### gcloud Commands - Cloud SQL

```bash
# Create Cloud SQL instance
gcloud sql instances create my-instance \
    --database-version=MYSQL_8_0 \
    --tier=db-n1-standard-1 \
    --region=us-central1

# List instances
gcloud sql instances list

# Create database
gcloud sql databases create mydb \
    --instance=my-instance

# Set root password
gcloud sql users set-password root \
    --host=% \
    --instance=my-instance \
    --password=MyPassword123

# Create user
gcloud sql users create myuser \
    --instance=my-instance \
    --password=MyPassword123

# Connect to instance
gcloud sql connect my-instance --user=root

# Create backup
gcloud sql backups create \
    --instance=my-instance

# Restore from backup
gcloud sql backups restore BACKUP_ID \
    --backup-instance=my-instance \
    --backup-project=PROJECT_ID

# Enable high availability
gcloud sql instances patch my-instance \
    --availability-type=REGIONAL

# Create read replica
gcloud sql instances create my-replica \
    --master-instance-name=my-instance \
    --tier=db-n1-standard-1 \
    --region=us-east1

# Delete instance
gcloud sql instances delete my-instance

# Get connection name
gcloud sql instances describe my-instance \
    --format='value(connectionName)'
```

### Cloud SQL Proxy (Secure Connection)

```bash
# Download Cloud SQL Proxy
wget https://dl.google.com/cloudsql/cloud_sql_proxy.linux.amd64 -O cloud_sql_proxy
chmod +x cloud_sql_proxy

# Start proxy
./cloud_sql_proxy -instances=PROJECT_ID:REGION:INSTANCE_NAME=tcp:3306

# Connect via localhost
mysql -u root -p --host 127.0.0.1
```

### 🎯 Interview Answers

**Q: When to use Cloud SQL vs Cloud Spanner?**
> "Cloud SQL for traditional relational workloads (MySQL/PostgreSQL/SQL Server), regional scale, lower cost. Cloud Spanner for globally distributed, horizontally scalable, strong consistency, higher cost."

**Q: How does Cloud SQL High Availability work?**
> "Regional HA with synchronous replication to standby in different zone. Automatic failover within seconds. Standby is not accessible (not a read replica)."

---

## ⚡ Cloud Functions (Serverless)

### Cloud Functions Generations

| Feature | 1st Gen | 2nd Gen (Recommended) |
|---------|---------|------------------------|
| **Runtime** | Cloud Functions | Cloud Run |
| **Max Timeout** | 9 minutes | 60 minutes |
| **Max Instances** | 3,000 | 1,000 |
| **Min Instances** | 0 | 0-1000 |
| **Concurrency** | 1 per instance | Up to 1000 |
| **Triggers** | Limited | More options |

### Supported Runtimes

- Node.js 20, 18, 16
- Python 3.12, 3.11, 3.10
- Go 1.21, 1.20
- Java 17, 11
- .NET 6, 8
- Ruby 3.2

### Sample Function (Python)

```python
import functions_framework

@functions_framework.http
def hello_world(request):
    """HTTP Cloud Function.
    Args:
        request (flask.Request): The request object.
    Returns:
        The response text, or any set of values that can be turned into a
        Response object using `make_response`.
    """
    name = request.args.get('name', 'World')
    return f'Hello, {name}!'
```

### gcloud Commands - Cloud Functions

```bash
# Deploy HTTP function
gcloud functions deploy my-function \
    --runtime=python312 \
    --trigger-http \
    --allow-unauthenticated \
    --entry-point=hello_world \
    --region=us-central1

# Deploy function with environment variables
gcloud functions deploy my-function \
    --runtime=python312 \
    --trigger-http \
    --set-env-vars=KEY1=value1,KEY2=value2

# Deploy Pub/Sub triggered function
gcloud functions deploy my-function \
    --runtime=python312 \
    --trigger-topic=my-topic \
    --entry-point=process_message

# Deploy Cloud Storage triggered function
gcloud functions deploy my-function \
    --runtime=python312 \
    --trigger-bucket=my-bucket \
    --entry-point=process_file

# List functions
gcloud functions list

# Describe function
gcloud functions describe my-function --region=us-central1

# Call function
gcloud functions call my-function \
    --data='{"name":"John"}' \
    --region=us-central1

# View logs
gcloud functions logs read my-function --region=us-central1

# Delete function
gcloud functions delete my-function --region=us-central1

# Deploy with specific memory/timeout
gcloud functions deploy my-function \
    --runtime=python312 \
    --trigger-http \
    --memory=512MB \
    --timeout=60s \
    --max-instances=100
```

### Common Triggers

- **HTTP**: REST API endpoints
- **Pub/Sub**: Message queue events
- **Cloud Storage**: Object create/delete/update
- **Firestore**: Database document changes
- **Cloud Scheduler**: Cron jobs
- **Eventarc**: Any Cloud event

### 🎯 Interview Answers

**Q: Cloud Functions 1st Gen vs 2nd Gen?**
> "2nd Gen runs on Cloud Run (better performance, longer timeout up to 60 min, higher concurrency). 1st Gen is legacy (9 min timeout, 1 request per instance). Always use 2nd Gen for new projects."

**Q: When to use Cloud Functions vs Cloud Run?**
> "Cloud Functions for simple event-driven tasks (auto-scales to zero, easier deployment). Cloud Run for containerized apps (more control, any language, longer requests, WebSockets)."

---

## ☸️ GKE (Google Kubernetes Engine)

### GKE Architecture

```
┌────────────────────────────────────────────────────────┐
│                    GKE Cluster                          │
├────────────────────────────────────────────────────────┤
│                                                          │
│  ┌──────────────────────┐         ┌─────────────────┐ │
│  │   Control Plane      │         │   Node Pools    │ │
│  │   (Managed by GCP)   │◄───────►│  (Compute VMs)  │ │
│  │                      │         │                 │ │
│  │  • API Server        │         │  ┌───────────┐  │ │
│  │  • etcd              │         │  │  Node 1   │  │ │
│  │  • Scheduler         │         │  │  Pods...  │  │ │
│  │  • Controller        │         │  └───────────┘  │ │
│  └──────────────────────┘         │                 │ │
│                                    │  ┌───────────┐  │ │
│  ✅ FREE Control Plane             │  │  Node 2   │  │ │
│  ✅ Auto-upgrade                   │  │  Pods...  │  │ │
│  ✅ Auto-repair                    │  └───────────┘  │ │
│                                    └─────────────────┘ │
│                                                          │
│  Modes: Standard (you manage) | Autopilot (GCP manages)│
└────────────────────────────────────────────────────────┘
```

### GKE Modes

| Feature | Standard | Autopilot |
|---------|----------|-----------|
| **Management** | You manage nodes | GCP manages nodes |
| **Node Config** | Full control | Predefined |
| **Pricing** | Pay for nodes | Pay for pods |
| **Scaling** | Manual/auto | Automatic |
| **Security** | You configure | Built-in hardening |
| **Use When** | Custom requirements | Hands-off, best practices |

### gcloud Commands - GKE

```bash
# Create GKE cluster (Standard mode)
gcloud container clusters create my-cluster \
    --zone=us-central1-a \
    --num-nodes=3 \
    --machine-type=e2-medium \
    --enable-autoscaling \
    --min-nodes=1 \
    --max-nodes=5

# Create GKE cluster (Autopilot mode)
gcloud container clusters create-auto my-autopilot-cluster \
    --region=us-central1

# Get cluster credentials
gcloud container clusters get-credentials my-cluster --zone=us-central1-a

# List clusters
gcloud container clusters list

# Describe cluster
gcloud container clusters describe my-cluster --zone=us-central1-a

# Resize cluster
gcloud container clusters resize my-cluster \
    --num-nodes=5 \
    --zone=us-central1-a

# Upgrade cluster
gcloud container clusters upgrade my-cluster \
    --master \
    --zone=us-central1-a

# Create node pool
gcloud container node-pools create gpu-pool \
    --cluster=my-cluster \
    --zone=us-central1-a \
    --machine-type=n1-standard-4 \
    --num-nodes=2 \
    --accelerator type=nvidia-tesla-k80,count=1

# Delete node pool
gcloud container node-pools delete gpu-pool \
    --cluster=my-cluster \
    --zone=us-central1-a

# Delete cluster
gcloud container clusters delete my-cluster --zone=us-central1-a

# Enable cluster autoscaling
gcloud container clusters update my-cluster \
    --enable-autoscaling \
    --min-nodes=1 \
    --max-nodes=10 \
    --zone=us-central1-a
```

### GKE Features

- **Free Control Plane**: No cost for Kubernetes masters
- **Autopilot Mode**: Fully managed nodes
- **Auto-Upgrade**: Automatic version updates
- **Auto-Repair**: Auto-fix unhealthy nodes
- **Workload Identity**: Use IAM for pods
- **Binary Authorization**: Deploy only trusted images
- **Cloud Monitoring**: Built-in observability
- **GKE Enterprise**: Multi-cluster management

### 🎯 Interview Answers

**Q: What makes GKE different from EKS and AKS?**
> "GKE invented by Google (created Kubernetes), easiest to use, free control plane, Autopilot mode (fully managed), fastest upgrades, best networking performance. Often considered the gold standard."

**Q: Standard vs Autopilot mode?**
> "Standard gives full control over nodes (choose size, scaling, SSH access). Autopilot is hands-off (GCP manages everything, pay only for pods, built-in security, zero node