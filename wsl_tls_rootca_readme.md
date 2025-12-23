# WSL SSL / TLS Fix Guide (Windows Corporate Root CA)

This guide explains why WSL (Ubuntu) may fail to access HTTPS sites due to **Windows-level TLS interception** on corporate environments and how to fix it securely.

---

## 🔹 Root Cause

* 100% Windows-level TLS interception / missing root CA on Windows
* Common on corporate laptops, VPNs, Zscaler, Palo Alto, FortiGate, CrowdStrike, etc.
* WSL inherits Windows networking but **does NOT inherit Windows trusted root certificates**

### 🔍 What is happening

```
WSL ──TLS──▶ Windows Network Stack ──(Intercepted)──▶ Internet
```

* Windows trusts the corporate root CA
* WSL does NOT, so every HTTPS request fails

This explains why:

* Google fails
* HashiCorp fails
* Fresh Ubuntu fails
* No proxy variables are visible

---

## ✅ Permanent Fix (Correct & Secure)

We must **import the Windows Root CA certificate into WSL**. This is the only correct solution.

---

## 🧱 Step 1 — Export Root Certificate from Windows

1. Press **Win + R**
2. Type:

```
certmgr.msc
```

3. Navigate to:

```
Trusted Root Certification Authorities → Certificates
```

4. Look for non-Microsoft root certs (corporate / VPN / security software), e.g.:

* Zscaler
* Palo Alto
* Fortinet
* CrowdStrike
* Company-specific
* Any suspicious/custom root cert

5. Right-click the corporate root cert → **All Tasks → Export**
6. Export as **Base-64 encoded X.509 (.CER)**
7. Save to:

```
C:\Users\nazmulhaque\Downloads\corp-root.cer
```

---

## 🧱 Step 2 — Copy Cert into WSL

In WSL (Ubuntu):

```bash
cp /mnt/c/Users/nazmulhaque/Downloads/corp-root.cer ~/
```

Verify:

```bash
ls -l ~/corp-root.cer
```

---

## 🧱 Step 3 — Install Certificate into Ubuntu Trust Store

```bash
sudo cp ~/corp-root.cer /usr/local/share/ca-certificates/corp-root.crt
sudo update-ca-certificates
```

You should see:

```
1 added, 0 removed
```

---

## 🧪 Step 4 — Test SSL

```bash
curl -I https://google.com
```

✅ Expected output:

```
HTTP/2 301
```

If this works, the problem is permanently solved.

---

## 🔒 Why this is Safe & Correct

* You are **not disabling SSL**
* You are trusting your **company’s root CA**
* Works with the **same model browsers and Windows**
* Required for **Terraform, AWS CLI, Git, Helm**

This is an **enterprise-grade DevOps setup**.
