# Kubernetes Setup in WSL (Docker + Kind)

You can run **Kubernetes inside WSL** using **Docker** and **Kind**, which is ideal for **development, testing, DevOps learning, and CI/CD pipelines**.

---

## Prerequisites

* WSL2 with Ubuntu 24.04
* Docker Engine installed inside WSL
* Sufficient RAM (at least 4GB recommended)

---

## Step 1: Install Kubernetes CLI (kubectl)

```bash
# Download kubectl
sudo curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
sudo chmod +x kubectl
sudo mv kubectl /usr/local/bin/

# Verify
kubectl version --client
```

---

## Step 2: Install Kind (Kubernetes in Docker)

```bash
# Download Kind
curl -Lo ./kind "https://kind.sigs.k8s.io/dl/v0.26.0/kind-linux-amd64"
chmod +x kind
sudo mv kind /usr/local/bin/
```

---

## Step 3: Create a Kubernetes Cluster with Kind

```bash
kind create cluster --name dev-cluster
kubectl cluster-info --context kind-dev-cluster
kubectl get nodes
```

**Notes:**

* Kind runs Kubernetes inside Docker containers
* You can create **multiple clusters** for testing
* Fully Linux-native inside WSL

---

### ✅ Summary

| Tool    | Purpose                                      |
| ------- | -------------------------------------------- |
| Docker  | Container runtime for Kind clusters          |
| Kind    | Multi-node Kubernetes clusters inside Docker |
| kubectl | Kubernetes CLI to interact with clusters     |

This setup allows you to run **Kubernetes locally inside WSL** without Docker Desktop, fully Linux-native and compatible with Terraform, Helm, and other DevOps tools.
