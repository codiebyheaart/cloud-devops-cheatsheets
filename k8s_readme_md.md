# 🚀 Kubernetes Quick Reference Guide

> **Your go-to cheat sheet for Kubernetes interviews and daily work**

[![Kubernetes](https://img.shields.io/badge/Kubernetes-326CE5?style=for-the-badge&logo=kubernetes&logoColor=white)](https://kubernetes.io/)
[![AWS](https://img.shields.io/badge/AWS_EKS-FF9900?style=for-the-badge&logo=amazon-aws&logoColor=white)](https://aws.amazon.com/eks/)
[![Azure](https://img.shields.io/badge/Azure_AKS-0078D4?style=for-the-badge&logo=microsoft-azure&logoColor=white)](https://azure.microsoft.com/en-us/products/kubernetes-service)
[![GCP](https://img.shields.io/badge/GCP_GKE-4285F4?style=for-the-badge&logo=google-cloud&logoColor=white)](https://cloud.google.com/kubernetes-engine)

---

## 📊 Big Picture Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    KUBERNETES CLUSTER                        │
├─────────────────────────────────────────────────────────────┤
│                                                               │
│  ┌──────────────────────┐      ┌──────────────────────┐    │
│  │   CONTROL PLANE      │      │    WORKER NODES      │    │
│  │   (Managed by Cloud) │      │    (VMs/Bare Metal)  │    │
│  │                      │      │                      │    │
│  │  • API Server        │      │  ┌─────────────┐    │    │
│  │  • etcd (Storage)    │◄────►│  │   NODE 1    │    │    │
│  │  • Scheduler         │      │  │             │    │    │
│  │  • Controller Mgr    │      │  │ Pod1 Pod2   │    │    │
│  └──────────────────────┘      │  │ Pod3 Pod4   │    │    │
│                                 │  │             │    │    │
│                                 │  │ + kubelet   │    │    │
│                                 │  │ + kube-proxy│    │    │
│                                 │  └─────────────┘    │    │
│                                 │                      │    │
│                                 │  ┌─────────────┐    │    │
│                                 │  │   NODE 2    │    │    │
│                                 │  │  (more pods)│    │    │
│                                 │  └─────────────┘    │    │
│                                 └──────────────────────┘    │
│                                                               │
│  Flow: Cluster → Nodes (VMs) → Pods (Containers)            │
└─────────────────────────────────────────────────────────────┘
```

**Remember this hierarchy:**
```
Kubernetes Cluster
    └── Nodes (Virtual Machines or Bare Metal)
        └── Pods (Smallest deployable unit)
            └── Containers (Your application)
```

---

## ☁️ Cloud Providers Quick Comparison

### AWS EKS (Elastic Kubernetes Service)
```bash
# Quick Setup
eksctl create cluster --name my-cluster --region us-east-1
```

| Feature | Details |
|---------|---------|
| **Load Balancer** | ALB (Application), NLB (Network) |
| **Storage** | EBS (Block), EFS (File System) |
| **Authentication** | IAM Roles & Policies |
| **Networking** | VPC CNI Plugin |
| **Best For** | Already using AWS services, tight AWS integration |

**💡 Quick Answer:** *"EKS is AWS's managed Kubernetes with deep integration into AWS services like IAM, EBS, and VPC."*

---

### Azure AKS (Azure Kubernetes Service)
```bash
# Quick Setup
az aks create --resource-group myRG --name myCluster --node-count 3
```

| Feature | Details |
|---------|---------|
| **Load Balancer** | Azure Load Balancer |
| **Storage** | Azure Disks, Azure Files |
| **Authentication** | Azure Active Directory |
| **Networking** | Azure CNI, Kubenet |
| **Best For** | Microsoft ecosystem, **best free tier**, Windows containers |

**💡 Quick Answer:** *"AKS integrates with Azure AD and Azure services, offers the best free tier among the three."*

---

### GCP GKE (Google Kubernetes Engine)
```bash
# Quick Setup
gcloud container clusters create my-cluster --zone us-central1-a
```

| Feature | Details |
|---------|---------|
| **Load Balancer** | Cloud Load Balancing |
| **Storage** | Persistent Disks, Filestore |
| **Authentication** | Google IAM |
| **Networking** | VPC-native, GKE networking |
| **Best For** | **Easiest to use**, Google services, best autopilot mode |

**💡 Quick Answer:** *"GKE is the easiest Kubernetes service, invented by Google (who created K8s), with excellent autopilot mode."*

---

## 🔄 Cloud Services Summary Table

| Feature | AWS EKS | Azure AKS | GCP GKE |
|---------|---------|-----------|---------|
| **Ease of Use** | ⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| **Free Tier** | ❌ ($0.10/hr) | ✅ Best | ✅ Good |
| **Autopilot** | ❌ | ❌ | ✅ Best |
| **Market Share** | 🥇 Largest | 🥈 Second | 🥉 Third |
| **Best Use** | AWS ecosystem | Microsoft stack | Beginners, GCP users |

**🎯 Interview Answer Template:**
> "All three provide managed Kubernetes with identical core components. **EKS** for AWS integration, **AKS** for Azure/Microsoft with best free tier, **GKE** for easiest setup and best autopilot mode. The choice depends on your existing cloud ecosystem."

---

## 🖥️ Why Not Always VMs?

### Comparison Table

| Type | Size | Startup Time | Cost | Isolation | Use Case |
|------|------|--------------|------|-----------|----------|
| **Virtual Machine** | GBs | Minutes | 💰💰💰 | ⭐⭐⭐⭐⭐ | Legacy apps, OS control |
| **Container** | MBs | Seconds | 💰 | ⭐⭐⭐ | Microservices, modern apps |
| **Bare Metal** | - | Hours | 💰💰💰💰 | ⭐⭐⭐⭐⭐ | HPC, databases |
| **Serverless** | - | Milliseconds | 💰 (pay-per-use) | ⭐⭐⭐⭐ | Event-driven, sporadic |

### Virtual Machines (Traditional)
```
✅ Pros:
  • Strong isolation (separate OS kernel)
  • Full OS-level control
  • Better for legacy applications
  • Can run different OS on same hardware

❌ Cons:
  • Heavy (GBs in size)
  • Slow startup (3-5 minutes)
  • Resource intensive
  • More expensive to run

🎯 Use When: Security-critical apps, stateful services, need OS isolation
```

### Containers (Modern - Kubernetes Default)
```
✅ Pros:
  • Lightweight (MBs in size)
  • Fast startup (seconds)
  • Portable across environments
  • Cost-effective
  • Perfect for microservices

❌ Cons:
  • Shared kernel (less isolation than VMs)
  • Must use same OS as host
  • Potential security concerns

🎯 Use When: Microservices, stateless apps, CI/CD, need agility
```

### Bare Metal
```
✅ Pros:
  • Maximum performance
  • No virtualization overhead
  • Direct hardware access

❌ Cons:
  • Expensive
  • Harder to manage
  • Less flexible

🎯 Use When: High-performance computing, large databases, gaming servers
```

### Serverless (AWS Fargate / Azure Container Instances / Cloud Run)
```
✅ Pros:
  • No node management
  • Pay only for actual usage
  • Auto-scaling built-in

❌ Cons:
  • Limited control
  • Cold start latency
  • Vendor lock-in

🎯 Use When: Event-driven workloads, sporadic traffic, want zero ops
```

**🎯 Interview Answer:**
> "Kubernetes typically runs on VMs for a balance of isolation and efficiency. Containers are lightweight and portable, while VMs provide stronger isolation. Bare metal is for performance-critical workloads like databases, and serverless (Fargate/Cloud Run) removes infrastructure management entirely."

---

## 🧩 Core Kubernetes Components

### Control Plane (Master - Managed by Cloud Provider)
| Component | Purpose | Analogy |
|-----------|---------|---------|
| **API Server** | Entry point for all commands | Reception desk |
| **etcd** | Stores cluster state & config | Database |
| **Scheduler** | Assigns pods to nodes | Traffic controller |
| **Controller Manager** | Maintains desired state | Thermostat |

### Worker Node Components
| Component | Purpose | Analogy |
|-----------|---------|---------|
| **kubelet** | Agent on each node | Site manager |
| **kube-proxy** | Network routing | Switchboard |
| **Container Runtime** | Runs containers (Docker/containerd) | Engine |

### Common Kubernetes Resources

```
📦 Pod
   └─ Smallest unit, wraps 1+ containers
   └─ Example: nginx pod with 1 container

🔄 Deployment
   └─ Manages pod replicas and rolling updates
   └─ Example: Web app with 3 replicas

🌐 Service
   └─ Exposes pods to network
   └─ Types: ClusterIP, NodePort, LoadBalancer
   └─ Example: LoadBalancer for external traffic

⚙️ ConfigMap
   └─ Non-sensitive configuration data
   └─ Example: Database connection strings

🔐 Secret
   └─ Sensitive data (base64 encoded)
   └─ Example: API keys, passwords

🚪 Ingress
   └─ HTTP/HTTPS routing rules
   └─ Example: Route /api → backend, /web → frontend

💾 PersistentVolume (PV) / PersistentVolumeClaim (PVC)
   └─ Storage that survives pod restarts
   └─ Example: Database data volume

📁 Namespace
   └─ Virtual cluster for resource isolation
   └─ Example: dev, staging, prod
```

---

## 🎯 Quick Decision Tree

```
Need to deploy an app?
└─► Use: Deployment + Service

Need external access?
└─► Use: Service (type: LoadBalancer) or Ingress

Need to store data permanently?
└─► Use: PersistentVolumeClaim (PVC)

Need configuration?
├─► Non-sensitive? → ConfigMap
└─► Sensitive? → Secret

Need to separate environments?
└─► Use: Namespaces (dev, staging, prod)

Need auto-scaling?
└─► Enable: HPA (Horizontal Pod Autoscaler)

Need to run background jobs?
├─► One-time job? → Job
└─► Recurring job? → CronJob
```

---

## 💻 Essential kubectl Commands

### Viewing Resources
```bash
# List all nodes
kubectl get nodes

# List all pods in all namespaces
kubectl get pods -A

# List deployments
kubectl get deployments

# Get detailed info about a pod
kubectl describe pod <pod-name>

# Watch pods in real-time
kubectl get pods -w
```

### Deployment Operations
```bash
# Apply configuration
kubectl apply -f deployment.yaml

# Scale deployment
kubectl scale deployment <name> --replicas=5

# Check rollout status
kubectl rollout status deployment/<name>

# Rollback to previous version
kubectl rollout undo deployment/<name>

# Delete deployment
kubectl delete deployment <name>
```

### Debugging
```bash
# View pod logs
kubectl logs <pod-name>

# Follow logs (live)
kubectl logs -f <pod-name>

# Shell into a pod
kubectl exec -it <pod-name> -- /bin/bash

# View cluster events
kubectl get events

# Check resource usage
kubectl top nodes
kubectl top pods
```

### Creating Resources Quickly
```bash
# Create a deployment imperatively
kubectl create deployment nginx --image=nginx --replicas=3

# Expose deployment as service
kubectl expose deployment nginx --port=80 --type=LoadBalancer

# Create namespace
kubectl create namespace dev
```

---

## 📝 Sample YAML Files

### Deployment Example
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-app
  labels:
    app: nginx
spec:
  replicas: 3                    # Number of pods
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.21
        ports:
        - containerPort: 80
        resources:
          requests:
            memory: "64Mi"
            cpu: "250m"
          limits:
            memory: "128Mi"
            cpu: "500m"
```

### Service Example
```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  type: LoadBalancer           # External access
  selector:
    app: nginx                 # Matches deployment labels
  ports:
  - port: 80                   # External port
    targetPort: 80             # Container port
```

### ConfigMap Example
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  database_url: "postgres://db:5432"
  log_level: "info"
```

### Secret Example
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: app-secrets
type: Opaque
data:
  api_key: YXBpLWtleS0xMjM0NTY=    # base64 encoded
  db_password: cGFzc3dvcmQxMjM=     # base64 encoded
```

---

## 🔥 Common Interview Questions

### Q1: Explain Kubernetes architecture
**Answer:** 
> "Kubernetes has a control plane that manages the cluster—it includes the API server (entry point), etcd (stores state), scheduler (assigns pods), and controller manager (maintains desired state). Worker nodes run the actual workloads as pods. Pods contain one or more containers and are the smallest deployable units. The kubelet agent on each node communicates with the control plane."

### Q2: What's the difference between Pod, Deployment, and Service?
**Answer:**
> "A **Pod** is a single instance of containers. A **Deployment** manages multiple pod replicas, handles rolling updates, and maintains desired state. A **Service** provides stable networking to access pods, as pod IPs change when they restart."

### Q3: How does Kubernetes scaling work?
**Answer:**
> "Three types: **HPA** (Horizontal Pod Autoscaler) scales pods based on CPU/memory metrics. **VPA** (Vertical Pod Autoscaler) adjusts resource limits. **Cluster Autoscaler** adds or removes nodes. You define target metrics and min/max replicas, and K8s handles the rest."

### Q4: What are the types of Kubernetes Services?
**Answer:**
> "**ClusterIP** (default) - internal cluster access only. **NodePort** - exposes service on each node's IP at a static port. **LoadBalancer** - provisions cloud load balancer for external access. **ExternalName** - maps service to external DNS name."

### Q5: How do you troubleshoot a failing pod?
**Answer:**
```bash
# Step 1: Check pod status
kubectl get pods

# Step 2: Describe pod for events
kubectl describe pod <pod-name>

# Step 3: Check logs
kubectl logs <pod-name>

# Step 4: Check previous logs if crashed
kubectl logs <pod-name> --previous

# Step 5: Shell into pod if running
kubectl exec -it <pod-name> -- /bin/bash
```

### Q6: Difference between ConfigMap and Secret?
**Answer:**
> "**ConfigMap** stores non-sensitive configuration like database URLs, feature flags. **Secret** stores sensitive data like passwords, API keys, and is base64 encoded (not encrypted by default—use encryption at rest for production)."

### Q7: What's a StatefulSet vs Deployment?
**Answer:**
> "**Deployment** is for stateless apps—pods are interchangeable. **StatefulSet** is for stateful apps like databases—pods have stable network IDs, persistent storage, and ordered deployment/scaling."

---

## 🚀 Scaling in Kubernetes

### Horizontal Pod Autoscaler (HPA)
```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: nginx-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: nginx-app
  minReplicas: 2
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 80
```

```bash
# Enable HPA via command
kubectl autoscale deployment nginx-app --cpu-percent=80 --min=2 --max=10
```

---

## 🎓 Remember These Key Points

1. **Cluster → Nodes → Pods → Containers** (hierarchy)
2. **Nodes are typically VMs**, but can be bare metal or serverless
3. **Pods are ephemeral** - they can die and restart anytime
4. **Services provide stable networking** to pods
5. **Deployments manage pod lifecycle** and rolling updates
6. **All three clouds (EKS/AKS/GKE) use the same core K8s components**
7. **YAML is declarative** - you describe desired state, K8s makes it happen
8. **Namespaces provide logical isolation** within a cluster
9. **ConfigMaps for config, Secrets for sensitive data**
10. **HPA scales horizontally** (more pods), VPA scales vertically (more resources)

---

## 📚 Infrastructure Requirements

### What You Need to Run Kubernetes:

```
☁️ Networking
├── VPC (Virtual Private Cloud)
├── Subnets (public/private)
├── Security Groups / Firewall Rules
└── Load Balancers

💾 Storage
├── Persistent Volumes (EBS, Azure Disks, GCP PD)
├── File Storage (EFS, Azure Files, Filestore)
└── Object Storage (S3, Blob, GCS)

🖥️ Compute
├── Worker Nodes (EC2, Azure VMs, GCE instances)
├── Control Plane (managed by cloud provider)
└── Container Registry (ECR, ACR, GCR)

🔐 Security
├── IAM Roles & Policies
├── SSL/TLS Certificates
└── Secrets Management
```

---

## 🎯 One-Liner Answers for Quick Recall

| Question | Quick Answer |
|----------|--------------|
| What is Kubernetes? | Container orchestration platform that automates deployment, scaling, and management |
| Why use K8s? | Automated scaling, self-healing, rolling updates, service discovery, load balancing |
| EKS vs AKS vs GKE? | EKS=AWS integration, AKS=best free tier, GKE=easiest to use |
| Pod vs Container? | Pod wraps containers, smallest K8s unit; containers are the actual app |
| Why not always VMs? | Containers are lighter (MBs vs GBs), faster (seconds vs minutes), cheaper |
| How does scaling work? | HPA scales pods, Cluster Autoscaler scales nodes, VPA adjusts resources |
| What's a Service? | Stable network endpoint to access pods (ClusterIP/NodePort/LoadBalancer) |
| ConfigMap vs Secret? | ConfigMap=config data, Secret=sensitive data (base64 encoded) |
| What's etcd? | Key-value store that holds all cluster state and configuration |
| What's kubelet? | Agent on each node that ensures containers are running as expected |

---

## 🔗 Useful Resources

- [Official Kubernetes Docs](https://kubernetes.io/docs/)
- [kubectl Cheat Sheet](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)
- [AWS EKS Documentation](https://docs.aws.amazon.com/eks/)
- [Azure AKS Documentation](https://docs.microsoft.com/azure/aks/)
- [GCP GKE Documentation](https://cloud.google.com/kubernetes-engine/docs)

---

## 📥 How to Use This Guide

1. **Star this repo** for quick access
2. **Clone locally** for offline reference
3. **Print the architecture diagram** and keep it visible
4. **Practice commands** in a local cluster (minikube/kind)
5. **Review before interviews** - focus on the quick answers section

---

## 🤝 Contributing

Found an error or want to add more? Feel free to submit a PR!

---

**Made with ❤️ for Kubernetes learners and interview prep**

*Last Updated: December 2025*