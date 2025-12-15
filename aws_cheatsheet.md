# ☁️ AWS (Amazon Web Services) Complete Cheat Sheet

> Your comprehensive guide to AWS services, commands, and best practices

[![AWS](https://img.shields.io/badge/AWS-FF9900?style=for-the-badge&logo=amazon-aws&logoColor=white)](https://aws.amazon.com/)

---

## 📋 Table of Contents
- [Core Services Overview](#core-services-overview)
- [EC2 (Compute)](#ec2-elastic-compute-cloud)
- [S3 (Storage)](#s3-simple-storage-service)
- [VPC (Networking)](#vpc-virtual-private-cloud)
- [IAM (Security)](#iam-identity--access-management)
- [RDS (Database)](#rds-relational-database-service)
- [Lambda (Serverless)](#lambda-serverless-computing)
- [EKS (Kubernetes)](#eks-elastic-kubernetes-service)
- [CloudFormation (IaC)](#cloudformation-infrastructure-as-code)
- [AWS CLI Commands](#aws-cli-quick-reference)

---

## 🎯 Core Services Overview

```
┌─────────────────────────────────────────────────────────────┐
│                        AWS CLOUD                             │
├─────────────────────────────────────────────────────────────┤
│                                                               │
│  COMPUTE              STORAGE              NETWORKING        │
│  ┌──────────┐        ┌──────────┐        ┌──────────┐      │
│  │   EC2    │        │    S3    │        │   VPC    │      │
│  │  Lambda  │        │   EBS    │        │Route 53  │      │
│  │   ECS    │        │   EFS    │        │CloudFront│      │
│  │   EKS    │        │          │        │          │      │
│  └──────────┘        └──────────┘        └──────────┘      │
│                                                               │
│  DATABASE             SECURITY            MANAGEMENT         │
│  ┌──────────┐        ┌──────────┐        ┌──────────┐      │
│  │   RDS    │        │   IAM    │        │CloudWatch│      │
│  │ DynamoDB │        │  Secrets │        │CloudTrail│      │
│  │ ElastiC  │        │   KMS    │        │          │      │
│  └──────────┘        └──────────┘        └──────────┘      │
│                                                               │
└─────────────────────────────────────────────────────────────┘
```

### Quick Service Selection Guide

| Need | Use This Service |
|------|------------------|
| Virtual Servers | **EC2** |
| Object Storage | **S3** |
| Block Storage | **EBS** |
| Content Delivery | **CloudFront** |
| Load Balancing | **ELB/ALB** |
| Serverless Functions | **Lambda** |
| Container Orchestration | **ECS/EKS** |
| SQL Database | **RDS** |
| NoSQL Database | **DynamoDB** |
| DNS Management | **Route 53** |
| Private Network | **VPC** |
| Access Management | **IAM** |
| Monitoring | **CloudWatch** |

---

## 💻 EC2 (Elastic Compute Cloud)

### Instance Types Quick Reference

| Type | Use Case | Example |
|------|----------|---------|
| **t3.micro** | Development, testing | Low traffic websites |
| **t3.medium** | Small applications | WordPress, small APIs |
| **m5.large** | General purpose | Web servers, medium apps |
| **c5.xlarge** | Compute optimized | High-performance computing |
| **r5.large** | Memory optimized | In-memory databases, cache |
| **p3.2xlarge** | GPU instances | Machine learning, gaming |

### EC2 Instance Lifecycle

```
┌──────────┐      Start      ┌──────────┐     Terminate    ┌────────────┐
│ Stopped  │ ─────────────► │ Running  │ ──────────────► │ Terminated │
└──────────┘                 └──────────┘                  └────────────┘
     ▲                            │
     │           Stop             │
     └────────────────────────────┘
```

### AWS CLI - EC2 Commands

```bash
# List all instances
aws ec2 describe-instances

# List running instances
aws ec2 describe-instances --filters "Name=instance-state-name,Values=running"

# Start an instance
aws ec2 start-instances --instance-ids i-1234567890abcdef0

# Stop an instance
aws ec2 stop-instances --instance-ids i-1234567890abcdef0

# Terminate an instance
aws ec2 terminate-instances --instance-ids i-1234567890abcdef0

# Create a new instance
aws ec2 run-instances \
    --image-id ami-0abcdef1234567890 \
    --instance-type t3.micro \
    --key-name MyKeyPair \
    --security-group-ids sg-0123456789abcdef0 \
    --subnet-id subnet-0123456789abcdef0

# Create key pair
aws ec2 create-key-pair --key-name MyKeyPair --query 'KeyMaterial' --output text > MyKeyPair.pem

# Describe security groups
aws ec2 describe-security-groups
```

### User Data Script (Auto-run on launch)

```bash
#!/bin/bash
yum update -y
yum install -y httpd
systemctl start httpd
systemctl enable httpd
echo "<h1>Hello from EC2!</h1>" > /var/www/html/index.html
```

### 🎯 Interview Answers

**Q: What are EC2 instance families?**
> "T-series for burstable general purpose, M-series for balanced compute/memory, C-series for compute-intensive, R-series for memory-intensive, and P/G-series for GPU workloads."

**Q: What's the difference between stopping and terminating?**
> "Stopping preserves the instance and EBS volume (you can restart it). Terminating permanently deletes the instance and root EBS volume (unless configured otherwise)."

---

## 🗄️ S3 (Simple Storage Service)

### S3 Storage Classes

| Class | Use Case | Availability | Cost |
|-------|----------|--------------|------|
| **Standard** | Frequently accessed | 99.99% | 💰💰💰 |
| **Intelligent-Tiering** | Unknown patterns | 99.9% | 💰💰 (auto) |
| **Standard-IA** | Infrequent access | 99.9% | 💰💰 |
| **One Zone-IA** | Non-critical, infrequent | 99.5% | 💰 |
| **Glacier Instant** | Archive, instant retrieval | 99.9% | 💰 |
| **Glacier Flexible** | Archive, minutes-hours | 99.99% | $ |
| **Glacier Deep Archive** | Long-term, 12hr retrieval | 99.99% | ¢ |

### AWS CLI - S3 Commands

```bash
# List all buckets
aws s3 ls

# List contents of a bucket
aws s3 ls s3://my-bucket/

# Create a bucket
aws s3 mb s3://my-unique-bucket-name

# Upload a file
aws s3 cp file.txt s3://my-bucket/

# Upload a folder recursively
aws s3 cp local-folder/ s3://my-bucket/folder/ --recursive

# Download a file
aws s3 cp s3://my-bucket/file.txt ./

# Sync local folder to S3
aws s3 sync ./local-folder s3://my-bucket/folder/

# Delete a file
aws s3 rm s3://my-bucket/file.txt

# Delete a bucket (must be empty)
aws s3 rb s3://my-bucket

# Make object public
aws s3 cp file.txt s3://my-bucket/ --acl public-read

# Enable versioning
aws s3api put-bucket-versioning --bucket my-bucket --versioning-configuration Status=Enabled

# Enable static website hosting
aws s3 website s3://my-bucket/ --index-document index.html --error-document error.html
```

### S3 Bucket Policy Example (Public Read)

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "PublicReadGetObject",
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::my-bucket/*"
    }
  ]
}
```

### 🎯 Interview Answers

**Q: What is S3 consistency model?**
> "S3 provides strong read-after-write consistency for all operations. After a successful write, any read will return the latest version."

**Q: How do you secure S3 buckets?**
> "Block public access by default, use bucket policies and IAM roles, enable versioning, use MFA Delete, enable encryption (SSE-S3, SSE-KMS), enable logging and CloudTrail."

---

## 🌐 VPC (Virtual Private Cloud)

### VPC Architecture

```
┌──────────────────────────────────────────────────────────┐
│                     VPC (10.0.0.0/16)                     │
│                                                            │
│  ┌─────────────────────┐    ┌─────────────────────┐     │
│  │  Public Subnet      │    │  Private Subnet     │     │
│  │  (10.0.1.0/24)      │    │  (10.0.2.0/24)      │     │
│  │                     │    │                     │     │
│  │  ┌──────┐ ┌──────┐ │    │  ┌──────┐ ┌──────┐ │     │
│  │  │ EC2  │ │ EC2  │ │    │  │ RDS  │ │Lambda│ │     │
│  │  │(web) │ │(web) │ │    │  │ (DB) │ │      │ │     │
│  │  └──────┘ └──────┘ │    │  └──────┘ └──────┘ │     │
│  │                     │    │                     │     │
│  │  Internet Gateway   │    │   NAT Gateway       │     │
│  │        ↕            │    │        ↕            │     │
│  └─────────────────────┘    └─────────────────────┘     │
│           ↕                                               │
└───────────────────────────────────────────────────────────┘
            ↕
      [Internet]
```

### VPC Components

| Component | Purpose |
|-----------|---------|
| **VPC** | Isolated network (10.0.0.0/16) |
| **Subnet** | Segment within VPC (public/private) |
| **Internet Gateway** | Connects VPC to internet |
| **NAT Gateway** | Allows private subnet internet access |
| **Route Table** | Defines network traffic routing |
| **Security Group** | Instance-level firewall (stateful) |
| **NACL** | Subnet-level firewall (stateless) |
| **VPC Peering** | Connect two VPCs |
| **VPN** | Encrypted connection to on-premises |
| **Direct Connect** | Dedicated physical connection |

### AWS CLI - VPC Commands

```bash
# Create VPC
aws ec2 create-vpc --cidr-block 10.0.0.0/16

# Create subnet
aws ec2 create-subnet --vpc-id vpc-123456 --cidr-block 10.0.1.0/24

# Create internet gateway
aws ec2 create-internet-gateway

# Attach internet gateway to VPC
aws ec2 attach-internet-gateway --vpc-id vpc-123456 --internet-gateway-id igw-123456

# Create security group
aws ec2 create-security-group --group-name my-sg --description "My security group" --vpc-id vpc-123456

# Add inbound rule (allow SSH)
aws ec2 authorize-security-group-ingress \
    --group-id sg-123456 \
    --protocol tcp \
    --port 22 \
    --cidr 0.0.0.0/0

# Describe VPCs
aws ec2 describe-vpcs
```

### Security Group vs NACL

| Feature | Security Group | NACL |
|---------|---------------|------|
| **Level** | Instance | Subnet |
| **State** | Stateful | Stateless |
| **Rules** | Allow only | Allow & Deny |
| **Evaluation** | All rules | Rules in order |
| **Default** | Deny all inbound | Allow all |

### 🎯 Interview Answers

**Q: What's the difference between public and private subnet?**
> "Public subnet has a route to Internet Gateway (0.0.0.0/0 → IGW), allowing direct internet access. Private subnet routes through NAT Gateway for outbound-only internet access."

**Q: Security Group vs NACL?**
> "Security Groups are stateful (return traffic auto-allowed), instance-level, and only support allow rules. NACLs are stateless, subnet-level, support both allow/deny rules, and are evaluated in order."

---

## 🔐 IAM (Identity & Access Management)

### IAM Components

```
┌─────────────────────────────────────────────┐
│              AWS Account                     │
│                                              │
│  ┌────────┐  ┌────────┐  ┌────────┐        │
│  │ Users  │  │ Groups │  │ Roles  │        │
│  └────────┘  └────────┘  └────────┘        │
│       │           │            │            │
│       └───────────┴────────────┘            │
│                   │                         │
│              ┌────────┐                     │
│              │Policies│                     │
│              └────────┘                     │
│          (JSON permissions)                 │
└─────────────────────────────────────────────┘
```

### IAM Best Practices

✅ **DO:**
- Enable MFA for root account
- Create individual IAM users
- Use groups to assign permissions
- Grant least privilege
- Use roles for applications
- Rotate credentials regularly
- Enable CloudTrail logging

❌ **DON'T:**
- Share credentials
- Use root account for daily tasks
- Embed access keys in code
- Grant `*` permissions broadly

### Sample IAM Policy (S3 Read-Only)

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:GetObject",
        "s3:ListBucket"
      ],
      "Resource": [
        "arn:aws:s3:::my-bucket",
        "arn:aws:s3:::my-bucket/*"
      ]
    }
  ]
}
```

### AWS CLI - IAM Commands

```bash
# List users
aws iam list-users

# Create user
aws iam create-user --user-name john

# Create access key
aws iam create-access-key --user-name john

# Attach policy to user
aws iam attach-user-policy --user-name john --policy-arn arn:aws:iam::aws:policy/ReadOnlyAccess

# List policies
aws iam list-policies --scope Local

# Create role
aws iam create-role --role-name MyRole --assume-role-policy-document file://trust-policy.json

# List roles
aws iam list-roles
```

### 🎯 Interview Answers

**Q: What's the difference between IAM User and Role?**
> "Users are for people with long-term credentials. Roles are for temporary access, used by services (EC2, Lambda) or federated users, and don't have permanent credentials."

**Q: Explain IAM policy structure**
> "Policies are JSON documents with Effect (Allow/Deny), Action (API calls), Resource (ARN), and optional Condition. They're attached to users, groups, or roles to grant permissions."

---

## 🗃️ RDS (Relational Database Service)

### Supported Database Engines

| Engine | Use Case | Max Storage |
|--------|----------|-------------|
| **MySQL** | General purpose, web apps | 64 TB |
| **PostgreSQL** | Advanced features, GIS | 64 TB |
| **MariaDB** | MySQL fork, open-source | 64 TB |
| **Oracle** | Enterprise applications | 64 TB |
| **SQL Server** | Windows/.NET apps | 16 TB |
| **Aurora** | AWS-optimized, 5x faster | 128 TB |

### RDS Features

- **Multi-AZ**: Automatic failover to standby in different AZ
- **Read Replicas**: Scale read traffic (up to 5 replicas)
- **Automated Backups**: Point-in-time recovery (1-35 days)
- **Snapshots**: Manual backups, kept indefinitely
- **Encryption**: At rest (KMS) and in transit (SSL)
- **Auto Scaling**: Storage auto-grows when needed

### AWS CLI - RDS Commands

```bash
# Create DB instance
aws rds create-db-instance \
    --db-instance-identifier mydb \
    --db-instance-class db.t3.micro \
    --engine mysql \
    --master-username admin \
    --master-user-password MyPassword123 \
    --allocated-storage 20

# List DB instances
aws rds describe-db-instances

# Create snapshot
aws rds create-db-snapshot \
    --db-snapshot-identifier mydb-snapshot \
    --db-instance-identifier mydb

# Delete DB instance
aws rds delete-db-instance \
    --db-instance-identifier mydb \
    --skip-final-snapshot

# Create read replica
aws rds create-db-instance-read-replica \
    --db-instance-identifier mydb-replica \
    --source-db-instance-identifier mydb
```

### 🎯 Interview Answers

**Q: What's the difference between Multi-AZ and Read Replicas?**
> "Multi-AZ is for high availability with synchronous replication to standby (automatic failover). Read Replicas are for scaling reads with asynchronous replication (can be in different regions)."

**Q: When to use RDS vs DynamoDB?**
> "Use RDS for complex queries, joins, transactions, and relational data. Use DynamoDB for high-scale, low-latency, key-value or document data with simple queries."

---

## ⚡ Lambda (Serverless Computing)

### Lambda Architecture

```
┌──────────┐      Trigger      ┌──────────┐      Execute      ┌──────────┐
│  Event   │ ────────────────► │  Lambda  │ ────────────────► │  Result  │
│ Source   │                    │ Function │                    │          │
└──────────┘                    └──────────┘                    └──────────┘
    │                                                                 │
    ├─ API Gateway                                                   ├─ S3
    ├─ S3                                                             ├─ DynamoDB
    ├─ DynamoDB                                                       ├─ SNS/SQS
    ├─ CloudWatch                                                     └─ External API
    └─ EventBridge
```

### Supported Runtimes

- Python 3.12, 3.11, 3.10
- Node.js 20.x, 18.x
- Java 21, 17, 11
- .NET 8, 6
- Go 1.x
- Ruby 3.3, 3.2
- Custom Runtime (Rust, etc.)

### Sample Lambda Function (Python)

```python
import json

def lambda_handler(event, context):
    # Parse input
    name = event.get('name', 'World')
    
    # Business logic
    message = f'Hello, {name}!'
    
    # Return response
    return {
        'statusCode': 200,
        'body': json.dumps({'message': message})
    }
```

### AWS CLI - Lambda Commands

```bash
# Create function
aws lambda create-function \
    --function-name my-function \
    --runtime python3.12 \
    --role arn:aws:iam::123456789012:role/lambda-role \
    --handler lambda_function.lambda_handler \
    --zip-file fileb://function.zip

# Invoke function
aws lambda invoke \
    --function-name my-function \
    --payload '{"name": "John"}' \
    response.json

# Update function code
aws lambda update-function-code \
    --function-name my-function \
    --zip-file fileb://function.zip

# List functions
aws lambda list-functions

# Get function
aws lambda get-function --function-name my-function

# Delete function
aws lambda delete-function --function-name my-function
```

### Lambda Limits

| Limit | Value |
|-------|-------|
| **Memory** | 128 MB - 10,240 MB |
| **Timeout** | 900 seconds (15 min) |
| **Deployment Package** | 50 MB (zipped), 250 MB (unzipped) |
| **/tmp Storage** | 512 MB - 10,240 MB |
| **Concurrent Executions** | 1,000 (default, can increase) |

### 🎯 Interview Answers

**Q: What are Lambda cold starts?**
> "Cold start is the latency when Lambda initializes a new execution environment (download code, start runtime). Warm starts reuse existing environments and are much faster. Keep functions small and use provisioned concurrency for consistent performance."

**Q: How does Lambda pricing work?**
> "Charged for number of requests and execution duration (GB-seconds). Free tier includes 1M requests and 400,000 GB-seconds per month. Very cost-effective for sporadic workloads."

---

## ☸️ EKS (Elastic Kubernetes Service)

### EKS Architecture

```
┌────────────────────────────────────────────────────────┐
│                    EKS Cluster                          │
├────────────────────────────────────────────────────────┤
│                                                          │
│  ┌──────────────────────┐         ┌─────────────────┐ │
│  │   Control Plane      │         │  Worker Nodes   │ │
│  │   (Managed by AWS)   │◄───────►│   (EC2/Fargate) │ │
│  │                      │         │                 │ │
│  │  • API Server        │         │  ┌───────────┐  │ │
│  │  • etcd              │         │  │ Node 1    │  │ │
│  │  • Scheduler         │         │  │ Pods...   │  │ │
│  │  • Controller        │         │  └───────────┘  │ │
│  └──────────────────────┘         │                 │ │
│                                    │  ┌───────────┐  │ │
│                                    │  │ Node 2    │  │ │
│                                    │  │ Pods...   │  │ │
│                                    │  └───────────┘  │ │
│                                    └─────────────────┘ │
│                                                          │
│  Integrated with: ALB, EBS, EFS, IAM, CloudWatch       │
└────────────────────────────────────────────────────────┘
```

### eksctl Commands

```bash
# Create cluster
eksctl create cluster \
    --name my-cluster \
    --region us-east-1 \
    --nodegroup-name standard-workers \
    --node-type t3.medium \
    --nodes 3

# Get cluster
eksctl get cluster --name my-cluster

# Update kubeconfig
aws eks update-kubeconfig --name my-cluster --region us-east-1

# Scale nodegroup
eksctl scale nodegroup \
    --cluster my-cluster \
    --name standard-workers \
    --nodes 5

# Delete cluster
eksctl delete cluster --name my-cluster
```

### EKS vs ECS

| Feature | EKS | ECS |
|---------|-----|-----|
| **Orchestrator** | Kubernetes | AWS Proprietary |
| **Portability** | Multi-cloud | AWS only |
| **Learning Curve** | Steep | Easier |
| **Community** | Large | AWS-focused |
| **Control Plane** | Paid | Free |
| **Use When** | K8s expertise, multi-cloud | AWS-native, simpler |

### 🎯 Interview Answers

**Q: What are the benefits of EKS over self-managed Kubernetes?**
> "AWS manages control plane (API server, etcd, scheduler), provides automatic upgrades, integrated with AWS services (IAM, VPC, ELB, EBS), and offers high availability across multiple AZs."

**Q: EKS vs ECS?**
> "EKS uses standard Kubernetes (portable, large community, steeper learning curve). ECS is AWS-proprietary (simpler, free control plane, AWS-only). Choose EKS for K8s expertise and multi-cloud strategy."

---

## 📦 CloudFormation (Infrastructure as Code)

### Sample CloudFormation Template

```yaml
AWSTemplateFormatVersion: '2010-09-09'
Description: 'Simple EC2 instance with security group'

Parameters:
  KeyName:
    Description: EC2 Key Pair
    Type: AWS::EC2::KeyPair::KeyName

Resources:
  MySecurityGroup:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupDescription: Allow SSH and HTTP
      SecurityGroupIngress:
        - IpProtocol: tcp
          FromPort: 22
          ToPort: 22
          CidrIp: 0.0.0.0/0
        - IpProtocol: tcp
          FromPort: 80
          ToPort: 80
          CidrIp: 0.0.0.0/0

  MyEC2Instance:
    Type: AWS::EC2::Instance
    Properties:
      ImageId: ami-0abcdef1234567890
      InstanceType: t3.micro
      KeyName: !Ref KeyName
      SecurityGroups:
        - !Ref MySecurityGroup
      UserData:
        Fn::Base64: |
          #!/bin/bash
          yum update -y
          yum install -y httpd
          systemctl start httpd

Outputs:
  InstanceId:
    Description: Instance ID
    Value: !Ref MyEC2Instance
  PublicIP:
    Description: Public IP
    Value: !GetAtt MyEC2Instance.PublicIp
```

### AWS CLI - CloudFormation Commands

```bash
# Create stack
aws cloudformation create-stack \
    --stack-name my-stack \
    --template-body file://template.yaml \
    --parameters ParameterKey=KeyName,ParameterValue=my-key

# Update stack
aws cloudformation update-stack \
    --stack-name my-stack \
    --template-body file://template.yaml

# Delete stack
aws cloudformation delete-stack --stack-name my-stack

# List stacks
aws cloudformation list-stacks

# Describe stack
aws cloudformation describe-stacks --stack-name my-stack

# Validate template
aws cloudformation validate-template --template-body file://template.yaml
```

### 🎯 Interview Answers

**Q: CloudFormation vs Terraform?**
> "CloudFormation is AWS-native (free, deep AWS integration, YAML/JSON). Terraform is multi-cloud (HCL syntax, larger community, state management). Choose CloudFormation for AWS-only, Terraform for multi-cloud."

---

## 🔧 AWS CLI Quick Reference

### Configuration

```bash
# Configure AWS CLI
aws configure

# Set default region
aws configure set region us-east-1

# List configuration
aws configure list

# Use profiles
aws configure --profile production
aws s3 ls --profile production
```

### Common Commands

```bash
# Get caller identity
aws sts get-caller-identity

# List regions
aws ec2 describe-regions

# Get account ID
aws sts get-caller-identity --query Account --output text

# Output formats
aws ec2 describe-instances --output json
aws ec2 describe-instances --output table
aws ec2 describe-instances --output text

# Query with JMESPath
aws ec2 describe-instances --query 'Reservations[*].Instances[*].[InstanceId,State.Name]'

# Dry run (test without executing)
aws ec2 run-instances --dry-run --image-id ami-123456
```

---

## 🎓 AWS Interview Quick Answers

| Question | Quick Answer |
|----------|--------------|
| What is AWS? | Cloud computing platform offering 200+ services (compute, storage, database, networking) |
| What is EC2? | Virtual servers in the cloud, pay-as-you-go pricing |
| What is S3? | Object storage service, 99.999999999% durability |
| What is VPC? | Isolated virtual network in AWS cloud |
| What is IAM? | Identity and access management, controls who can access what |
| Multi-AZ vs Multi-Region? | Multi-AZ is within one region for HA, Multi-Region is across regions for DR |
| What is EBS? | Block storage for EC2 instances (like hard drives) |
| What is EFS? | Managed NFS file system, shared across multiple EC2 |
| What is CloudWatch? | Monitoring and logging service for AWS resources |
| What is CloudTrail? | Audit log of all AWS API calls |
| What is Route 53? | AWS DNS service with health checks and routing policies |
| What is ELB? | Elastic Load Balancer, distributes traffic across targets |
| Stateful vs Stateless? | Stateful remembers session (Security Group), Stateless doesn't (NACL) |

---

## 💰 AWS Cost Optimization Tips

1. **Use Reserved Instances** - Save up to 75% for predictable workloads
2. **Right-size resources** - Don't over-provision EC2/RDS
3. **Use S3 Intelligent-Tiering** - Auto-move to cheaper storage classes
4. **Delete unused resources** - EBS volumes, snapshots, EIPs
5. **Use Auto Scaling** - Scale down during low traffic
6. **Use Spot Instances** - Save up to 90% for fault-tolerant workloads
7. **Enable Cost Allocation Tags** - Track costs per project/team
8. **Set up Billing Alerts** - Get notified of unexpected costs
9. **Use AWS Cost Explorer** - Analyze spending patterns
10. **Leverage Free Tier** - 750 hours/month of t2.micro for 12 months

---

## 🔗 Useful Resources

- [AWS Documentation](https://docs.aws.amazon.com/)
- [AWS CLI Reference](https://docs.aws.amazon.com/cli/)
- [AWS Free Tier](https://aws.amazon.com/free/)
- [AWS Architecture Icons](https://aws.amazon.com/architecture/icons/)
- [AWS Well-Architected Framework](https://aws.amazon.com/architecture/well-architected/)

---

**Last Updated: December 2025**