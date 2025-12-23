# WSL DevOps Setup Guide (Ubuntu 24.04)

This guide explains how to set up **Windows Subsystem for Linux (WSL)** with **Ubuntu 24.04 LTS** and install essential **DevOps tools** including:

* Git
* Python
* Terraform
* Terragrunt
* AWS CLI

This setup mirrors a **production Linux environment** and is recommended for **AWS / DevOps / Cloud Engineers**.

---

## ✅ Prerequisites

* Windows 10 (21H2+) or Windows 11
* Administrator access on Windows
* Internet access

---

## 1️⃣ Install WSL with Ubuntu 24.04

Open **PowerShell as Administrator** and run:

```powershell
wsl --install -d Ubuntu-24.04
```

Restart Windows if prompted.

After installation, Ubuntu will launch and ask you to:

* Create a Linux username
* Set a password

---

## 2️⃣ Verify Internet & SSL (Important)

Inside Ubuntu, run:

```bash
curl -I https://google.com
```

### Expected output:

```
HTTP/1.1 301 Moved Permanently
```

If this fails with an **SSL certificate error**, your Windows system likely uses **corporate TLS inspection**.
👉 **Import your company’s root CA certificate into WSL** before continuing. follow the wsl_tls_rootca_readme.md file in this repo.

---

## 3️⃣ Update System Packages

```bash
sudo apt update && sudo apt upgrade -y
```

---

## 4️⃣ Install Core Utilities

```bash
sudo apt install -y \
  git \
  python3 \
  python3-pip \
  python3-venv \
  unzip \
  jq \
  make \
  ca-certificates
```

Verify:

```bash
git --version
python3 --version
pip3 --version
```

---

## 5️⃣ Install Terraform (Official HashiCorp Repo)

```bash
sudo apt install -y gnupg software-properties-common curl

curl -fsSL https://apt.releases.hashicorp.com/gpg \
  | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg

echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] \
https://apt.releases.hashicorp.com $(lsb_release -cs) main" \
| sudo tee /etc/apt/sources.list.d/hashicorp.list

sudo apt update
sudo apt install -y terraform
```

Verify:

```bash
terraform version
```

---

## 6️⃣ Install Terragrunt

```bash
TG_VERSION=$(curl -s https://api.github.com/repos/gruntwork-io/terragrunt/releases/latest \
  | grep tag_name | cut -d '"' -f 4)

curl -Lo terragrunt \
  https://github.com/gruntwork-io/terragrunt/releases/download/${TG_VERSION}/terragrunt_linux_amd64

chmod +x terragrunt
sudo mv terragrunt /usr/local/bin/
```

Verify:

```bash
terragrunt --version
```

---

## 7️⃣ Install AWS CLI v2

```bash
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o awscliv2.zip
unzip awscliv2.zip
sudo ./aws/install
```

Verify:

```bash
aws --version
```

---

## 8️⃣ Configure AWS Credentials (WSL)

```bash
aws configure
```

⚠️ AWS credentials configured in **Windows** are **not shared** with WSL.

---

## 9️⃣ Configure Git (Optional but Recommended)

```bash
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```

### SSH Key Setup

```bash
ssh-keygen -t ed25519 -C "your.email@example.com"
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519
cat ~/.ssh/id_ed25519.pub
```

Add the public key to **GitHub / GitLab**.

---

## 🔟 Recommended Workspace Structure

```bash
mkdir -p ~/workspace/{terraform,terragrunt,python,scripts}
cd ~/workspace
```

### Best Practice

✅ Work inside `/home/<user>`
❌ Avoid `/mnt/c` for Terraform and scripts (performance & permission issues)

---

## 🧠 Best Practices

* Use **WSL as your primary DevOps environment**
* Keep Terraform state remote (S3 + DynamoDB)
* Use Python virtual environments
* Do not disable SSL verification
* Keep tools installed **inside WSL**, not Windows

---

## 🚀 Next Steps

Recommended next installations:

* `kubectl`
* `helm`
* Docker Desktop (with WSL integration)
* Terraform + Terragrunt project templates
* CI/CD pipelines (GitHub Actions / GitLab CI)

---

## 📌 Supported OS

* Ubuntu 24.04 LTS on WSL2

---
