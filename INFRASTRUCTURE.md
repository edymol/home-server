
# Local AWS-like Infrastructure Setup Guide

## Step 1: Install Proxmox VE for Virtual Machine Management
Proxmox VE is a powerful open-source solution for managing virtual machines and containers, similar to AWS EC2.

### Download and Install Proxmox VE:
1. Go to [Proxmox VE download page](https://www.proxmox.com/en/proxmox-ve) and download the ISO file.
2. Burn the ISO to a USB drive and install it on your server machine.

### Initial Setup:
- After installation, configure the network interface, create storage, and set up virtual machine templates for future use.

### Create Your First Virtual Machine:
- From the Proxmox web UI, create a new virtual machine and allocate resources (CPU, memory, and storage). You can install an operating system like Ubuntu or any OS of your choice inside the VM.

---

## Step 2: Set Up Kubernetes for Container Orchestration
Kubernetes will manage your containers and serve as a foundation for your microservices and applications.

### Install k3s (Lightweight Kubernetes):
- On a VM or directly on your server, install k3s by running the following command:
```bash
curl -sfL https://get.k3s.io | sh -
```

### Verify the Installation:
- Check that Kubernetes is up and running by using the `kubectl` command:
```bash
kubectl get nodes
```

### Deploy a Sample Application:
- Deploy a simple Nginx application to ensure Kubernetes is functional:
```bash
kubectl create deployment nginx --image=nginx
```

- Expose it as a service:
```bash
kubectl expose deployment nginx --type=LoadBalancer --port=80
```

---

## Step 3: Install MinIO for Object Storage (Equivalent to AWS S3)
MinIO will serve as your local object storage solution, similar to S3.

### Install MinIO:
```bash
wget https://dl.min.io/server/minio/release/linux-amd64/minio
chmod +x minio
sudo mv minio /usr/local/bin/
```

### Start MinIO:
```bash
minio server /mnt/data
```

- This will start MinIO on http://localhost:9000. You can log in to the web interface and manage your storage buckets like S3.

### Set up MinIO Credentials:
- Access the MinIO web interface using the default username `minioadmin` and password `minioadmin`.

---

## Step 4: Set Up Terraform for Infrastructure as Code
Terraform will allow you to automate and manage the infrastructure.

### Install Terraform:
- Download and install Terraform from the [official Terraform site](https://www.terraform.io/downloads).

### Create Terraform Configuration for Proxmox:
```hcl
provider "proxmox" {
  pm_api_url = "https://proxmox.local:8006/api2/json"
  pm_user    = "root@pam"
  pm_password = "yourpassword"
}

resource "proxmox_vm_qemu" "vm1" {
  name        = "terraform-vm"
  target_node = "pve"
  disk {
    size    = 10
    storage = "local-lvm"
  }
  network {
    model  = "virtio"
    bridge = "vmbr0"
  }
}
```

### Run Terraform:
- Initialize Terraform and apply the configuration:
```bash
terraform init
terraform apply
```

---

## Step 5: Set Up Prometheus and Grafana for Monitoring
To monitor your infrastructure, set up Prometheus to collect metrics and Grafana for visualization.

### Install Prometheus:
```bash
sudo apt-get install prometheus
```

### Install Grafana:
```bash
sudo apt-get install grafana
```

### Access Grafana Dashboard:
- Once installed, you can access Grafana at http://localhost:3000 and configure Prometheus as a data source.

---

## Step 6: Authentication and Access Control with Keycloak
### Install Keycloak:
- Run Keycloak inside a Docker container:
```bash
docker run -d -p 8080:8080 -e KEYCLOAK_USER=admin -e KEYCLOAK_PASSWORD=admin jboss/keycloak
```

### Access Keycloak:
- Access Keycloak at http://localhost:8080 to configure users, roles, and applications.

---

## Step 7: Set Up Local DNS with CoreDNS
CoreDNS will manage your local DNS resolution, similar to AWS Route 53.

### Install CoreDNS in your Kubernetes cluster:
```bash
kubectl apply -f https://storage.googleapis.com/kubernetes-the-hard-way/coredns.yaml
```

---

## Step 8: Automate CI/CD with Jenkins or GitHub Actions
- For deployment automation, you can set up Jenkins on a VM or use GitHub Actions with OIDC to deploy applications to your Kubernetes cluster.
