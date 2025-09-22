# Kubernetes Theory Reference Guide

## Table of Contents
1. [Docker Theory](#1-docker-theory)
2. [Why Kubernetes?](#2-why-kubernetes)
3. [Kubernetes Architecture](#3-kubernetes-architecture)
4. [Core Kubernetes Concepts](#4-core-kubernetes-concepts)
5. [Networking Theory](#5-networking-theory)
6. [Configuration & Storage Concepts](#6-configuration--storage-concepts)
7. [Azure Container Registry (ACR)](#7-azure-container-registry-acr)
8. [Azure Kubernetes Service (AKS)](#8-azure-kubernetes-service-aks)

---

## 1. Docker Theory

### What is Containerization?
**Containerization** is a method of packaging applications along with all their dependencies (libraries, runtime, system tools) into a single, portable unit called a container.

### Container vs Virtual Machine
**Virtual Machine Approach:**
- Each application runs on a separate operating system
- Heavy resource consumption (each VM needs full OS)
- Slower startup times
- More isolation but higher overhead

**Container Approach:**
- Multiple applications share the same OS kernel
- Lightweight and efficient resource usage
- Fast startup times (seconds vs minutes)
- Process-level isolation

### Key Docker Concepts
- **Image**: A template/blueprint for creating containers
- **Container**: A running instance of an image
- **Dockerfile**: Text file with instructions to build an image
- **Registry**: Storage location for images (Docker Hub, ACR)

### Benefits of Containerization
- **Portability**: "Works on my machine" problem solved
- **Consistency**: Same environment from development to production
- **Scalability**: Easy to scale up/down
- **Resource Efficiency**: Better hardware utilization
- **Isolation**: Applications don't interfere with each other

---

## 2. Why Kubernetes?

### Problems with Manual Container Management
When you have multiple containers running manually:

**Scaling Issues:**
- Need to manually start/stop containers based on demand
- No automatic scaling based on CPU/memory usage
- Difficult to distribute load evenly

**High Availability Problems:**
- If a container crashes, manual intervention needed
- No automatic failover mechanisms
- Single points of failure

**Networking Complexity:**
- Containers need to discover and communicate with each other
- Manual load balancing setup required
- Complex service discovery

**Update Challenges:**
- Difficult to update applications without downtime
- Risk of breaking the entire system during updates
- No easy rollback mechanism

### What Kubernetes Solves

**Orchestration:**
- Automates container deployment and management
- Handles container scheduling across multiple machines
- Manages container lifecycle automatically

**Self-Healing:**
- Automatically restarts failed containers
- Replaces unhealthy containers
- Reschedules containers when nodes fail

**Auto-Scaling:**
- Scales applications up/down based on demand
- Can scale both applications and infrastructure
- Optimizes resource utilization

**Load Balancing:**
- Automatically distributes traffic across containers
- Provides service discovery mechanisms
- Handles routing and load distribution

**Rolling Updates:**
- Updates applications without downtime
- Gradual rollout with health checks
- Easy rollback if issues occur

---

## 3. Kubernetes Architecture

### Cluster Overview
A **Kubernetes Cluster** consists of:
- **Control Plane** (Master): Makes global decisions about the cluster
- **Worker Nodes**: Run the actual applications

### Control Plane Components (The Brain)

#### API Server
- **Role**: Central management hub and communication point
- **Function**: Processes all REST operations and updates cluster state
- **Analogy**: Like a company's receptionist - all requests go through here
- **Interaction**: kubectl, web UI, and all other components talk to API server

#### etcd
- **Role**: Distributed, consistent key-value store
- **Function**: Stores all cluster configuration and state data
- **Analogy**: Like a company's central filing system
- **Importance**: If etcd fails, cluster loses its "memory"

#### Scheduler
- **Role**: Resource manager for pod placement
- **Function**: Decides which worker node should run each pod
- **Considerations**: Resource requirements, hardware constraints, affinity rules
- **Analogy**: Like a hotel manager assigning rooms to guests

#### Controller Manager
- **Role**: Cluster state management
- **Function**: Ensures actual state matches desired state
- **Types**: Deployment Controller, ReplicaSet Controller, Service Controller
- **Analogy**: Like supervisors ensuring work is done correctly

### Worker Node Components (The Workers)

#### Kubelet
- **Role**: Node agent that manages pod lifecycle
- **Function**: Communicates with control plane, manages containers
- **Responsibilities**: Starting/stopping pods, health checks, resource monitoring
- **Analogy**: Like a factory floor manager overseeing operations

#### Kube-proxy
- **Role**: Network proxy running on each node
- **Function**: Maintains network rules and handles load balancing
- **Purpose**: Enables service discovery and communication
- **Analogy**: Like a telephone switchboard routing calls

#### Container Runtime
- **Role**: Software that runs containers
- **Options**: Docker, containerd, CRI-O
- **Function**: Pulls images and runs containers
- **Interface**: Works through Container Runtime Interface (CRI)

### Kubernetes Architecture Diagram

```
                     kubectl
                        │
                        ▼
        ┌─────────────────────────────────┐
        │            Master               │
        │                                 │
        │  ┌──────────┐   ┌─────────────┐ │
        │  │Scheduler │   │   etcd      │ │
        │  │    ⚙️     │◄──┤  (database) │ │
        │  └──────────┘   └─────────────┘ │
        │         │              ▲        │
        │         ▼              │        │
        │  ┌─────────────────────┼──────┐ │
        │  │      API Server     │      │ │
        │  │         ⚙️          │      │ │
        │  └─────────────────────┼──────┘ │
        │         │              │        │
        │         ▼              │        │
        │  ┌──────────────────────┘      │ │
        │  │   Controller Manager        │ │
        │  │         ⚙️                   │ │
        │  └─────────────────────────────┘ │
        └─────────────────┼───────────────┘
                          │
          ┌───────────────┼───────────────┐
          │               ▼               │
┌─────────┼─────────┐           ┌─────────┼─────────┐
│         │  Node   │           │         │  Node   │
│  ┌──────▼──────┐  │           │  ┌──────▼──────┐  │
│  │   Kubelet   │  │           │  │   Kubelet   │  │
│  │     ⚙️       │  │           │  │     ⚙️       │  │
│  └─────────────┘  │           │  └─────────────┘  │
│         │          │           │         │        │
│  ┌──────▼──────┐  │           │  ┌──────▼──────┐  │
│  │ Service     │  │           │  │ Service     │  │
│  │ Proxy ⚙️    │◄─┼───────────┼──┤ Proxy ⚙️    │  │
│  └─────────────┘  │           │  └─────────────┘  │
│                    │           │                  │
│  [📦] [📦] Pods    │           │  [📦] [📦] Pods  │
└────────────────────┘           └──────────────────┘
                          │
    ┌─────────────────────┼─────────────────────┐
    │       CNI Network (Weave Net, Calico)     │
    └───────────────────────────────────────────┘
                          ▲
                    👤 User Access
```

### Communication Flow
1. **User** sends request to **API Server** (via kubectl)
2. **API Server** validates and stores request in **etcd**
3. **Controllers** detect changes and take action
4. **Scheduler** assigns pods to nodes
5. **Kubelet** on nodes receives instructions and manages containers
6. **Service Proxy** handles networking and load balancing
7. **CNI Network** enables pod-to-pod communication across nodes

---

## 4. Core Kubernetes Concepts

### Pod
**Definition**: The smallest deployable unit in Kubernetes

**Key Characteristics:**
- Usually contains one container (can have multiple)
- Containers in a pod share network and storage
- Pods are ephemeral (temporary and disposable)
- Each pod gets its own IP address
- Pods are mortal - they're created, they live, they die

**Why Pods, Not Just Containers?**
- Provides shared context (network, storage)
- Enables helper containers (sidecars)
- Atomic unit of scheduling and deployment

### ReplicaSet
**Definition**: Ensures a specified number of pod replicas are running

**Purpose:**
- Maintains desired number of pods
- Replaces failed pods automatically
- Provides horizontal scaling capability

**Relationship**: Usually managed by Deployments, not created directly

### Deployment
**Definition**: Higher-level concept that manages ReplicaSets

**Key Features:**
- Declarative updates for pods and ReplicaSets
- Rolling update and rollback capabilities
- Revision history tracking
- Pause and resume functionality

**Benefits:**
- Zero-downtime deployments
- Easy rollback to previous versions
- Controlled rollout strategies

### Service
**Definition**: Stable network endpoint for accessing pods

**Problem Solved:** Pod IPs are dynamic and change when pods restart

**Service Types:**
- **ClusterIP**: Internal access only (default)
- **NodePort**: Exposes service on each node's IP at a static port
- **LoadBalancer**: Exposes service externally using cloud load balancer
- **ExternalName**: Maps service to external DNS name

### Namespace
**Definition**: Virtual clusters within a physical cluster

**Purpose:**
- Logical separation and organization
- Resource isolation and access control
- Multi-tenancy support

**Default Namespaces:**
- `default`: Default namespace for objects
- `kube-system`: System components
- `kube-public`: Publicly readable resources

---

## 5. Networking Theory

### Kubernetes Network Model
**Fundamental Requirements:**
- All pods can communicate with all other pods without NAT
- All nodes can communicate with all pods without NAT
- The IP that a pod sees itself as is the same IP that others see it as

### Pod-to-Pod Communication
- Each pod gets its own unique IP address
- Pods on the same node communicate through localhost
- Pods on different nodes communicate through cluster network
- No port conflicts between pods

### Service Discovery
**Problem**: Pod IPs change when pods are recreated
**Solution**: Services provide stable virtual IPs and DNS names

**DNS-Based Discovery:**
- Each service gets a DNS name
- Format: `service-name.namespace.svc.cluster.local`
- Automatic DNS resolution within cluster

### Network Policies
**Purpose**: Control traffic flow between pods
**Default**: All pods can communicate with all pods
**Implementation**: Define ingress and egress rules

---

## 6. Configuration & Storage Concepts

### Configuration Management Philosophy
**Principle**: Separate configuration from application code
**Benefits**: Same image can run in different environments

### ConfigMap
**Purpose**: Store non-confidential configuration data
**Use Cases:** Application settings, environment variables, configuration files
**Format**: Key-value pairs or file content
**Consumption**: Environment variables, command-line arguments, or volume files

### Secret
**Purpose**: Store sensitive information (passwords, tokens, keys)
**Security Features:**
- Base64 encoded (not encrypted by default)
- Can be encrypted at rest
- Access controlled through RBAC
- Mounted as files or environment variables

### Storage Concepts

#### Volume Types
- **Ephemeral Volumes**: Exist only during pod lifetime
- **Persistent Volumes**: Exist beyond pod lifetime
- **Projected Volumes**: Combine multiple volume sources

#### Persistent Volume (PV)
**Definition**: Cluster-level storage resource
**Lifecycle**: Independent of pods
**Provisioning**: Static (pre-created) or Dynamic (created on-demand)

#### Persistent Volume Claim (PVC)
**Definition**: User's request for storage
**Function**: Binds to available Persistent Volume
**Analogy**: Like a hotel reservation request

#### Storage Classes
**Purpose**: Define different types of storage
**Function**: Enable dynamic provisioning
**Examples**: Fast SSD, slow HDD, replicated storage

---

## 7. Azure Container Registry (ACR)

### What is ACR?
**Azure Container Registry** is a managed Docker registry service based on Docker Registry 2.0.

### Key Features
**Private Registry:**
- Secure, private storage for container images
- Integration with Azure Active Directory
- Role-based access control

**Geo-Replication:**
- Replicate images across multiple Azure regions
- Reduce latency for global deployments
- High availability and disaster recovery

**Security Features:**
- Image vulnerability scanning
- Content trust and image signing
- Network access control

### ACR Tiers
**Basic:** Essential features for individual developers
**Standard:** Increased storage and throughput
**Premium:** Highest performance, geo-replication, advanced security

### Integration Benefits
- **AKS Integration**: Seamless authentication and image pulling
- **Azure DevOps**: Built-in CI/CD pipeline integration
- **Azure Security Center**: Vulnerability assessments

---

## 8. Azure Kubernetes Service (AKS)

### What is AKS?
**Azure Kubernetes Service** is a managed Kubernetes service that simplifies deploying and managing containerized applications.

### Managed vs Self-Managed Kubernetes

**Self-Managed Kubernetes:**
- You manage all components (master and worker nodes)
- Responsible for upgrades, patches, and maintenance
- Full control but high operational overhead

**AKS (Managed):**
- Azure manages the control plane (master nodes)
- You only manage worker nodes
- Automatic updates and patches available
- Reduced operational complexity

### Key AKS Features

#### Control Plane Management
- **Free Control Plane**: No cost for Kubernetes masters
- **High Availability**: Multi-master setup available
- **Automatic Updates**: Managed updates for control plane components

#### Node Management
- **Virtual Machine Scale Sets**: Automatic scaling capabilities
- **Multiple Node Pools**: Different VM types for different workloads
- **Spot Instances**: Cost optimization with Azure Spot VMs

#### Integrated Services
- **Azure Active Directory**: Authentication and authorization
- **Azure Monitor**: Built-in monitoring and logging
- **Azure Policy**: Governance and compliance
- **Azure DevOps**: CI/CD integration

### Network Integration
**Azure CNI**: Advanced networking with VNet integration
**Kubenet**: Basic networking with simpler setup
**Private Clusters**: API server accessible only from private network

### Security Features
- **Azure AD Integration**: Role-based access control
- **Pod Security Policies**: Control pod security contexts
- **Network Policies**: Micro-segmentation within cluster
- **Azure Key Vault**: Secret management integration

### Scaling Capabilities
**Cluster Autoscaler**: Automatically adjusts node count
**Horizontal Pod Autoscaler**: Scales pods based on metrics
**Vertical Pod Autoscaler**: Adjusts resource requests/limits

### Cost Management
- **Pay only for worker nodes**: Control plane is free
- **Spot instances**: Up to 90% cost savings
- **Reserved instances**: Long-term cost optimization
- **Resource quotas**: Control resource consumption

---

## Conceptual Learning Flow

### Understanding Progression
1. **Containers** → Understand application packaging
2. **Container Orchestration Need** → Why manual management fails
3. **Kubernetes Architecture** → How the system is organized
4. **Core Objects** → Building blocks (Pod, Deployment, Service)
5. **Networking** → How components communicate
6. **Configuration** → How to manage application settings
7. **Cloud Integration** → ACR for images, AKS for hosting

### Key Mental Models
- **Desired State**: You declare what you want, Kubernetes makes it happen
- **Controllers**: Continuously work to match actual state to desired state
- **Labels and Selectors**: How Kubernetes objects find each other
- **Abstraction Layers**: Each layer abstracts complexity from the one above

### Common Misconceptions to Address
- Pods are not containers (they contain containers)
- Services don't run applications (they provide access to pods)
- Kubernetes doesn't make applications highly available by default
- Container images should be stateless and immutable

This theoretical foundation provides the conceptual understanding needed before diving into practical implementation!
