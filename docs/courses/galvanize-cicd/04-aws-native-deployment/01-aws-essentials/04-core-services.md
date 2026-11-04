# Core Services

## Core AWS Services and the Bigger Picture

## Learning Objectives 

- Explain the breadth of AWS offerings and the importance of focusing on core services 
- Identify the key AWS services commonly used in cloud-native applications
- Describe how core services interact with each o ther and fit into a CI/CD deployment model 

## Introduction 

This lesson focuses on the foundational services used to compute, store, deliver, and manage application infrastructure.

## The Big Four Categories 

Most core AWS services fall into one of four categories: 

- **Compute** - Where your code runs 
- **Storage** - Where your data lives
- **Networking** - How traffic flows
- **Databases** - Structured or unstructured data stores

We'll look at representative services from each category and show how they're used in real deployments. 

## Amazon EC2

**Elastic Compute Cloud (EC2)** provides virtual machines (called instances) that you can configure and run in the cloud. You choose an operating system, instance type, network configuration, and IAM role. 

### Use cases 

- Legacy workloads that require full OS access 
- Hosting Spring Boot APIs, databases, and custom runtime environments
- Deployments that don't yet use containers of serverless patterns

### Considerations

- You manage uptime, patching, and scaling
- You pay while the instance is running, even if idle
- CI/CD pipelines can launch, configure, and deploy to EC2 usinsg Terraform and GitLab jobs 

## Amazon S3

**Simeple Storage Service (S3)** is object storage in the cloud. You can store any file (images, logs, builds, configs) and retrieve it with a unique URL. 

### Use Cases 

- Hosting static websites
- Storing frontend build artifacts
- Holding app uploads and backups
- Exchanging files between servers

### Concepts

- **Buckets**: Top-level containers for objects (files)
- **Objects**: The actual files 
- **Prefixes**: Optional folder-like organization
- **Permissions**: Managed through IAM, bucket policies, or ACLs

## AWS Lambda 

**Lamnda** is a serverless compute service. You write a function, define triggers, and AWS runs your code on demand - no servers, scaling, or patching required. 

### Use Cases 

- REST APIs (often paired with API Gateway)
- Event-driven automation (S3, uploads, database writes, schedules)
- Lightweight backend logic with minimal infrastructure overhead

### Benefits

- No idle cost - you pay per request and execution time 
- Auto-scaling is built in 
- Integrates with IAM roles for securer access to other AWS services

## Amazon API Gateway 

**API Gateway** is the frontend for Lambda functions or other HTTP endpoints. It handles request routing, authentication, and throttling for your APIs. 

### Use cases 

- Exposing severless functions as REST APIs
- Creating secure, public-facing endpoints for internal services
- Throttling or validating incoming traffic before it hits your backend

### Integration pattern 

```text
Client -> API Gateway -> Lambda -> AWS Services (e.g., S3, DynamoDB)
``` 

## Amazon RDS and DynamoDB

AWS offers both relational and non-relational databases as managed services. 

### Amazon RDS 

- Managed SQL databases (PostgreSQL, MySQL, MariaDB, SQL Server, etc.)
- Automated backups, failover, and patching 
- Often used in legacy or transactional applications

### DynamoDB

- NoSQL key-value store
- Millisecond latency at scale 
- Great for serverless applications and variable schema requirements 

## Amazon EKS / ROSA

**Elastic Kubernetes Service (EKS)** and **Red Hat Openshift on AWS (ROSA)** are fully managed Kubernetes platforms. Both allows you to run containerized applications at scale without managing the Kubernets control plane. 

### Use cases

- Deploying modern microservices and containerized workloads 
- Running GitLab, CI/CD runners, or internal tools on Kubernetes
- Providing developers with a secure, flexible platform for building and deploying applications

### Concepts

- **Pods** run your containerized applications
- **Clusters** group and isolate workloads
- **IAM roles** can be mapped to Kubernetes service accounts (IRSA) for secure, fine-grained access to AWS resources 
- **ROSA** adds enterprise-grade tools like OpenShift's web console, integrated CI/CD, and policy management, often used in regulated environments

## Summary 

While AWS offers hundreds of services, most cloud-native application stacks rely on a small gropu of core tools. EC2, Lamnda, and EKS/ROSA provide compute options. S3 handles storage. API Gateway routes traffic. RDS and DynamoDB offer managed databases. These services are the building blocks of real-world architecture and appear repeatedly in infrastructure modules, GitLab jobs, and Terraform plans. 


