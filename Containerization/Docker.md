# Containerization

Transitioned Prometheus from traditional system service to Docker container to learn containerization concepts. 

## 1. Docker Installation and Setup

### Installation Process

```bash
# Install Docker on Ubuntu
sudo apt update
sudo apt install [docker.io](http://docker.io)
```

### Why Docker for Monitoring?

- **Isolation**: Applications run in separate containers
- **Portability**: Same container runs on any Linux system
- **Version Management**: Easy to test different Prometheus versions
- **Resource Efficiency**: Lower overhead than virtual machines

## 2. Container vs Virtual Machine Concept

**Virtual Machine Approach**:

- Each VM has complete OS (kernel, libraries, applications)
- Higher resource usage (RAM, CPU, storage)
- Slower startup times

**Container Approach**:

- Shares host OS kernel
- Only includes application and dependencies
- Faster startup, lower resource usage

**Analogy**:

- **VM**: Like building separate houses for each application
- **Container**: Like shipping containers that share the same cargo ship

## 3. Prometheus Containerization Process

### Problem Encountered: Permission Issues

**Issue**: Container user (nobody/UID 65534) couldn't write to host directories

**Error**: `open /prometheus/queries.active: permission denied`

**Root Cause**: User ID mismatch between container and host filesystem

### Solution Approaches Evaluated

**Selected Solution**: **Docker Named Volumes** (recommended for beginners)

## 4. Implementation Steps

### Step 1: Service Management

```bash
# Stop existing Prometheus systemd service
sudo systemctl stop prometheus
sudo systemctl disable prometheus
```

**Note**: Grafana continues running normally during this transition

### Step 2: Configuration Management

```bash
# Create config directory with proper ownership
mkdir -p ~/prometheus-config
sudo cp /etc/prometheus/prometheus.yml ~/prometheus-config/
sudo chown $USER:$USER ~/prometheus-config/prometheus.yml
```

### Step 3: Docker Volume Creation

```bash
# Create Docker-managed volume (handles permissions automatically)
docker volume create prometheus-data

# Verify volume creation
docker volume ls
```

**Key Concept**: Docker volumes abstract storage management from host filesystem

### Step 4: Container Deployment

```bash
docker run -d \
  --name prometheus-container \
  --network host \
  -v ~/prometheus-config/prometheus.yml:/etc/prometheus/prometheus.yml:ro \
  -v prometheus-data:/prometheus \
  prom/prometheus:latest \
  --config.file=/etc/prometheus/prometheus.yml \
  --storage.tsdb.path=/prometheus
```

**Command Breakdown**:

- `docker run -d`: Run container in background (daemon mode)
- `--name prometheus-container`: Assign container name for easy management
- `--network host`: Use host networking (container shares host IP)
- `-v ~/prometheus-config/prometheus.yml:/etc/prometheus/prometheus.yml:ro`: Mount config file (read-only)
- `-v prometheus-data:/prometheus`: Mount Docker volume for data storage
- `prom/prometheus:latest`: Official Prometheus Docker image
- `--config.file` & `--storage.tsdb.path`: Prometheus-specific configuration

## 5. Docker Installation on Amazon Linux 2023

```bash
# System update
sudo dnf update -y

# Docker installation
sudo dnf install -y docker
sudo systemctl start docker
sudo systemctl enable docker

# User permissions
sudo usermod -a -G docker ec2-user
newgrp docker

# Verification
docker --version
docker ps
```

## 6. Configuration Transfer Challenges & Solutions

**Initial Approach**: `scp` with SSH key authentication

**Issues Encountered**:

- PuTTY `.ppk` format incompatible with Linux `scp` (libcrypto error)
- Key conversion failed (`ssh-keygen -i` reported invalid format)
- `puttygen` not available on Amazon Linux 2023

**Resolution**: Manual configuration transfer (standard IT practice)

```bash
# On source server (Ubuntu)
cat ~/prometheus-config/prometheus.yml

# On target server (Amazon Linux)
mkdir -p ~/prometheus-config
nano ~/prometheus-config/prometheus.yml
# Paste content, save (Ctrl+X, Y, Enter)

# Verification
cat ~/prometheus-config/prometheus.yml
```

## 7. Container Registry with AWS ECR

### Overview

Implemented AWS Elastic Container Registry (ECR) to create a centralized private container repository, enabling true container portability across multiple servers.

### Workflow: Ubuntu Server → ECR → Amazon Linux Server

### Step 1: Create ECR Repository

```bash
# AWS Console: ECR → Create Repository
# Repository name: docker-repo
# Settings: Private, Mutable, AES-256 encryption
# Result: [465548141882.dkr.ecr.us-east-1.amazonaws.com/docker-repo](http://465548141882.dkr.ecr.us-east-1.amazonaws.com/docker-repo)
```

### Step 2: Create Custom Container Image

```bash
# On Ubuntu server - commit running container to image
sudo docker commit prometheus-container my-custom-prometheus

# Verify image creation
docker images
```

### Step 3: Configure AWS CLI & Push to ECR

```bash
# Install and configure AWS CLI
sudo snap install aws-cli --classic
aws configure  # Enter Access Key ID, Secret, us-east-1, json

# Login to ECR
aws ecr get-login-password --region us-east-1 | sudo docker login --username AWS --password-stdin [465548141882.dkr.ecr.us-east-1.amazonaws.com](http://465548141882.dkr.ecr.us-east-1.amazonaws.com)

# Tag container for ECR
sudo docker tag my-custom-prometheus [465548141882.dkr.ecr.us-east-1.amazonaws.com/docker-repo:prometheus-v1](http://465548141882.dkr.ecr.us-east-1.amazonaws.com/docker-repo:prometheus-v1)

# Push to ECR registry
sudo docker push [465548141882.dkr.ecr.us-east-1.amazonaws.com/docker-repo:prometheus-v1](http://465548141882.dkr.ecr.us-east-1.amazonaws.com/docker-repo:prometheus-v1)
```

### Step 4: Pull and Deploy on Target Server

```bash
# On Amazon Linux server - configure AWS CLI
aws configure  # Same credentials as source server

# Login to ECR
aws ecr get-login-password --region us-east-1 | sudo docker login --username AWS --password-stdin [465548141882.dkr.ecr.us-east-1.amazonaws.com](http://465548141882.dkr.ecr.us-east-1.amazonaws.com)

# Pull container from ECR
sudo docker pull [465548141882.dkr.ecr.us-east-1.amazonaws.com/docker-repo:prometheus-v1](http://465548141882.dkr.ecr.us-east-1.amazonaws.com/docker-repo:prometheus-v1)

# Deploy container
sudo docker run -d --name prometheus-from-ecr --network host [465548141882.dkr.ecr.us-east-1.amazonaws.com/docker-repo:prometheus-v1](http://465548141882.dkr.ecr.us-east-1.amazonaws.com/docker-repo:prometheus-v1)
```

### Key Benefits Achieved

- **True Portability**: Complete application with configurations ships as single unit
- **Central Repository**: ECR provides private, secure container storage
- **Version Control**: Tagged images enable rollback and version management
- **Infrastructure as Code**: Reproducible deployments across environments
- **Enterprise Workflow**: Matches production container deployment patterns

### Verification

Prometheus UI accessible at [`http://target-server-ip:9090`](http://target-server-ip:9090) with identical configuration and historical data preserved.
