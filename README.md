
# Local AWS-like Environment on Ubuntu Server

This guide will help you set up a local cloud environment that mimics AWS services on your Ubuntu Server. You can host websites, applications, and run CI/CD pipelines using GitHub Actions.

## Core Technologies
| AWS Equivalent      | Local/On-Premise Equivalent        |
|---------------------|-----------------------------------|
| EC2 (VMs/Instances) | OpenStack, Proxmox, Kubernetes    |
| S3 (Object Storage) | MinIO, Ceph                       |
| RDS (Databases)     | PostgreSQL, MySQL (self-hosted)   |
| EKS (Kubernetes)    | Kubernetes (k3s, k8s)            |
| VPC (Networking)    | OpenStack Neutron, Calico, Flannel|
| CloudWatch          | Prometheus + Grafana              |
| IAM (Identity)      | Keycloak, Vault, RBAC             |
| Route 53 (DNS)      | CoreDNS, Bind9                   |

## Step 1: Update and Upgrade Your System
```bash
sudo apt update && sudo apt upgrade -y
```

## Step 2: Install Proxmox for Virtual Machines
- Proxmox provides virtualization to run VMs (EC2 equivalent).
- Download and install Proxmox [here](https://www.proxmox.com/en/downloads).
- Access Proxmox interface at: `https://your-server-ip:8006`.

## Step 3: Install Kubernetes (k3s)
- Kubernetes manages containers, acting as your EC2 instances.
- Install k3s (lightweight Kubernetes):
```bash
curl -sfL https://get.k3s.io | sh -
```
- Use kubectl to manage your cluster.

## Step 4: Install MinIO (Object Storage, S3 Equivalent)
```bash
wget https://dl.min.io/server/minio/release/linux-amd64/minio
chmod +x minio
sudo mv minio /usr/local/bin/
minio server /mnt/data
```
- Access MinIO at: `http://your-server-ip:9000`.

## Step 5: Install Calico for Kubernetes Networking (VPC Equivalent)
```bash
kubectl apply -f https://docs.projectcalico.org/v3.14/manifests/calico.yaml
```
- This enables networking and isolation in your Kubernetes cluster.

## Step 6: Install PostgreSQL (RDS Equivalent)
```bash
sudo apt update
sudo apt install postgresql postgresql-contrib
sudo systemctl start postgresql
sudo -u postgres createuser --interactive
sudo -u postgres createdb your_database
```

## Step 7: Install Prometheus and Grafana (Monitoring)
- Install Prometheus:
```bash
sudo apt-get install prometheus
```
- Install Grafana:
```bash
sudo apt-get install grafana
```
- Access Grafana at `http://your-server-ip:3000`.

## Step 8: Install Keycloak for Identity and Access Management (IAM)
```bash
docker run -d -p 8080:8080 -e KEYCLOAK_USER=admin -e KEYCLOAK_PASSWORD=admin jboss/keycloak
```
- Keycloak helps manage users and access control.

## Step 9: Install Terraform for Infrastructure as Code
```bash
sudo apt-get update && sudo apt-get install -y gnupg software-properties-common curl
curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo apt-key add -
sudo apt-add-repository "deb [arch=amd64] https://apt.releases.hashicorp.com $(lsb_release -cs) main"
sudo apt-get update && sudo apt-get install terraform
```

## Step 10: Configure GitHub Actions for CI/CD
- Create a `.github/workflows` directory in your repo.
- Sample GitHub Actions workflow for Kubernetes deployment:
```yaml
name: Deploy to k3s
on:
  push:
    branches:
      - main
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Code
        uses: actions/checkout@v2
      - name: Set up k3s
        run: |
          curl -sfL https://get.k3s.io | sh -
      - name: Deploy Application
        run: |
          kubectl apply -f deployment.yaml
```

## Step 11: Install CoreDNS for DNS Management (Route 53 Equivalent)
```bash
kubectl apply -f https://storage.googleapis.com/kubernetes-the-hard-way/coredns.yaml
```

---
## Additional Resources:
- [Proxmox Documentation](https://www.proxmox.com/en/documentation)
- [Kubernetes Documentation](https://kubernetes.io/docs/)
- [MinIO Documentation](https://min.io/docs/minio/linux/index.html)
- [Keycloak Documentation](https://www.keycloak.org/documentation)
