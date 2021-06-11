# Implement container-based applications

## Offerings

- Azure Container Instances (ACI)
  - Quickest and easiest way to run a container in Azure
  - Serverless - don't need to provision infrastructure
  - Great for quick experiments
  - Pay only while your container is running
    - Per-second billing model (GB/s of RAM, vCPU/s)
	- There's an additional cost for Windows containers, depending on the region
- Azure Web App for Containers
- Azure Service Fabric
- Azure Kubernetes Service (AKS)

## Running Containers on Azure Container Instances

### ACI Use Cases

- Continuous integration build agents
  - Scale out to support multiple concurrent builds
- Short-lived experiments
  - Try out a technology, perform a load test
- Batch jobs
  - Run for a few hours overnight
- Elastic scale for Kubernetes clusters
  - "Virtual kubelet" lets you treat ACI as a virtual node in your k8s cluster, giving it infinite scale

Not for permanently running servers

### ACI Features

- Easy to create and manage
- Networking
  - Public IP address
  - Domain name prefix
  - Expose ports
- Windows or Linux containers (Linux container images are smaller)
- Restart policy 
  - Discard it when it is stopped?
- Mount volumes
  - Azure file share
  - Git repository
- Specify the command to run
- Configure environment variables
- Access container logs
- "Container groups"
  - One or more containers
  - Run on the same server and share resources

### Configuration

When specifying environment variables on the az cli, use --secure-environment-variables rather than --environment-variables to ensure the env-variables are stored securely.

## Running Containers on Web App for Containers

Shares most of the features available with regular web apps

Why Web App for Containers

- Consistent deployment model
- Support more frameworks
- Control over framework versions (can't do this with an App Service)
- Multi-container support
  - docker-compose or k8s YAML
- Sandboxed environment
- Trigger deployments from a container registry

## Running Containers on Azure Service Fabric

Azure Service Fabric and Azure Kubernetes Service are for deploying microservices

### Challenges of Microservices

- Deployment
- Health Monitoring
- Scaling out multiple services
- Service to service communication
- Upgrades
- Recover from hardware failures
- Orchestrators can help us
  - Azure Service Fabric

### Azure Service Fabric

- An "application platform"
  - Scalable and reliable microservices
- Hosting options
  - On-premises or other cloud providers
  - Development laptop
  - Azure
- Cluster
  - Monitors service health

### Programming Models

- Stateful
  - Co-locate compute and data
  - Reliable collections (you need to use these in your code)
 - Stateless services
  - Web APIs or executables
  - Containers
- Linux and Windows containers
  - It's one of the most mature platforms for Windows containers
  - Constrain RAM and CPU allocation
  - Docker-compose YAML support
		
### Service Fabric Benefits

- Powers many key Azure services
  - E.g. Cortana, Skype, Cosmos DB, and Power BI
- Why choose Service Fabric
  - Microservices applications
  - Windows containers
  - Ability to deploy outside Azure
  - Orchestration features


# Configure Azure Kubernetes Service

## Kubernetes Basics

- A "production grade container orchestration system"
- Cluster
  - Master nodes schedule containers
  - Worker nodes run containers
- Kubectl (Kubernetes Control)
- Pod - one or more containers
- ReplicaSet - how many instances of a pod should be running in a cluster
- Deployment - running code in k8s
- Service - load balancing
- Namespaces - isolation
- YAML - declarative deployments
- Helm - package manager for k8s

## Introducing Azure Kubernetes Service

- Managed Kubernetes Service
  - Control plane is free
  - Only pay for worker nodes (master nodes are free)
  - Simplified version upgrades
  - 100% upstream Kubernetes, the same software as regular kubernetes

## Integration with Azure Services

- Azure Monitor
- Mount Azure file shares or disks
- Secure with RBAC and AD
- Virtual network integration
- Elastic scale with ACI
- AKS is expected to support running Windows containers in the future
- Develop and debug with Azure Dev Spaces
  - This lets Visual Studio or Visual Studio Code connect to an AKS cluster and debug your container 
  - Lets you deploy your containers to a shared cluster, but your containers can be only visible to you

## Demo: Creating an AKS Cluster

- Need to specify:
  - Cluster Name
  - Region
  - Kubernetes Version
  - DNS prefix
