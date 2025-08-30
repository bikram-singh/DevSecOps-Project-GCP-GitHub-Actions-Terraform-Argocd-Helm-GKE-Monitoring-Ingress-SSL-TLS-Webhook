# DevSecOps Project – GCP + GitHub Actions + Terraform + ArgoCD + Helm + GKE + Monitoring + Ingress + SSL/TLS

## 🚀 Getting Started Quick Commands

```bash
# Authenticate with GCP
gcloud auth login
gcloud config set project devsecops-project-111

# Get GKE cluster credentials
gcloud container clusters get-credentials terraform-gke-cluster --region us-central1 --project devsecops-project-111

# Verify
kubectl get nodes
kubectl get pods
```

---

## 📌 Project Overview
This project demonstrates a **complete DevSecOps pipeline** on **Google Cloud Platform (GCP)** using:

- **Infrastructure as Code (IaC)** with **Terraform**
- **CI/CD pipeline** with **GitHub Actions**
- **Security scanning** using **Trivy, SonarQube, OWASP Dependency Check**
- **Kubernetes (GKE)** for container orchestration
- **Helm** for application deployment
- **ArgoCD** for GitOps-based continuous delivery
- **Prometheus & Grafana** for monitoring and observability
- **Nginx Ingress + Cert-Manager** for domain management and SSL/TLS
- **Webhooks** for automation triggers

---

## 📐 Architecture Diagram
*(Placeholder – add diagram.png here)*

---

## ✅ Prerequisites
- Google Cloud Project (`devsecops-project-111`)
- VPC & Subnet (`devsecops-vpc`, `devsecops-subnet`)
- RHEL8 VM for self-hosted GitHub Runner
- Installed tools on runner:
  - Docker
  - GCP SDK (`gcloud`)
  - Terraform
  - Helm
  - Trivy
  - SonarScanner
  - OWASP Dependency Check

---

## 📂 Project Structure
```
.
├── app/                         # Application source code
│   ├── index.html
│   ├── Dockerfile
│   └── README.md
│
├── helm/                        # Helm charts
│   ├── Chart.yaml
│   ├── values.yaml
│   └── templates/
│
├── gke-terraform/               # Terraform for GKE
│   ├── main.tf
│   ├── variables.tf
│   └── outputs.tf
│
├── reports/                     # Security scan outputs
│
├── .github/workflows/           # GitHub Actions pipeline
│   └── ci-infra.yml
│
├── .gitignore
├── README.md
└── LICENSE
```

---

## ⚙️ Setup Instructions
1. **Create GCP Project, VPC, Subnet, Firewall Rules**  
2. **Create Self-Hosted Runner VM (RHEL8)**  
   - Install Docker, Terraform, Helm, Trivy, SonarQube, GCP SDK  
   - Register runner in GitHub  
3. **Install and Configure Tools**
   - Run SonarQube in Docker (`docker run -d -p 9000:9000 sonarqube:lts-community`)
   - Configure SonarQube project & tokens
   - Install OWASP Dependency Check (local or via Docker)
4. **Deploy GKE Cluster via Terraform**
   ```bash
   cd gke-terraform
   terraform init
   terraform apply -auto-approve
   ```
5. **Install ArgoCD**
   ```bash
   kubectl create namespace argocd
   kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
   ```
6. **Configure Ingress + Domain + SSL/TLS**
   - Install Nginx Ingress Controller via Helm  
   - Point domain (e.g., `gcpcloudhub.in`) to LoadBalancer IP  
   - Install Cert-Manager and configure Let’s Encrypt issuer  

---

## 🛠 Terraform Usage
- Separate **Dev** and **Prod** using GitHub branches (`dev`, `main`)  
- GitHub Actions triggers Terraform `apply/destroy` via workflow inputs  
- Example:
```bash
terraform init
terraform plan
terraform apply -auto-approve
```

---

## 🔄 CI/CD Pipeline (GitHub Actions)
**Workflow: `.github/workflows/ci-infra.yml`**

- **CI (Application Build & Scan)**
  - Trivy Filesystem Scan
  - SonarQube Analysis + Quality Gate
  - OWASP Dependency Check
  - Docker Build & Push → GCP Artifact Registry
  - Trivy Image Scan
  - Helm Chart Update
  - HTML Summary Report
- **Infra (Terraform Pipeline)**
  - Apply or Destroy GKE Infrastructure

---

## 🚀 ArgoCD Deployment Flow
1. Install ArgoCD on GKE  
2. Change `argocd-server` service from `ClusterIP` → `LoadBalancer`  
3. Connect GitHub repository to ArgoCD  
4. ArgoCD auto-syncs Helm charts → Deploys updated images from Artifact Registry  

---

## 🎯 Helm Charts Usage
- Defined under `/helm`  
- Values updated dynamically by GitHub Actions (new Docker image tag pushed automatically)  
- Example deployment:
```bash
helm install hello ./helm -n default
```

---

## 🌐 Ingress & SSL/TLS Setup
1. Install **NGINX Ingress Controller**
2. Configure DNS in domain provider (e.g., Hostinger) to LoadBalancer IP
3. Install **Cert-Manager** and Let’s Encrypt ClusterIssuer
4. Annotate Ingress resource:
```yaml
annotations:
  nginx.ingress.kubernetes.io/force-ssl-redirect: "true"
  cert-manager.io/cluster-issuer: letsencrypt-prod
```

---

## 📊 Monitoring & Alerts
- Install **Prometheus & Grafana** via Helm
```bash
kubectl create namespace monitoring
helm install prometheus prometheus-community/prometheus -n monitoring
helm install grafana grafana/grafana -n monitoring
```
- Change Grafana service → `LoadBalancer`  
- Import dashboards (IDs: **315, 6417, 1860**)  
- Connect Prometheus datasource:  
  `http://prometheus-server.monitoring.svc.cluster.local:80`

---

## 🔔 Webhooks
- GitHub Actions workflows can be triggered manually or by **push to branch**  
- ArgoCD auto-sync acts as a webhook-driven deployment mechanism  

---

## 🔒 Security Best Practices
- Use **IAP** or **Cloud NAT** instead of wide `0.0.0.0/0` firewall rules  
- Store **secrets** in GitHub → `Settings > Secrets and Variables`  
- Regular vulnerability scans with Trivy + OWASP  
- Enforce **SonarQube Quality Gates**  

---

## 🤝 Contributing / Future Improvements
- Add multi-environment Helm chart values (`dev`, `staging`, `prod`)  
- Automate rollback strategy in ArgoCD  
- Integrate policy enforcement with **OPA/Gatekeeper**  

---


