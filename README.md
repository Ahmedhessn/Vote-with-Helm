# 🗳️ Vote Application – Terraform + Kubernetes + Helm

This project deploys the **Vote Application** using a complete DevOps workflow that includes:

- **Terraform** → to provision the infrastructure (AKS / Kubernetes Cluster)
- **Kubernetes YAML Manifests** → for low-level configuration (Deployments, Services, Secrets…)
- **Helm Charts** → for packaging and deploying the Vote App in a reusable and scalable way

---

## 📌 Project Architecture

The system follows the classic **microservices architecture** used in the Vote App:

| Component | Description |
|----------|-------------|
| **Vote App** | Frontend + Backend application |
| **Redis** | Caching layer |
| **PostgreSQL / MySQL (optional)** | Persistent storage |
| **Worker** | Background job processor |
| **Result App** | Displays the voting results |
| **Ingress Controller** | External access to the application |
| **AKS (Azure Kubernetes Service)** | Managed Kubernetes cluster |

---

## 📁 Repository Structure

Vote-with-Helm/
│
├── terraform/ # Terraform IaC to deploy AKS & Networking
│ ├── main.tf
│ ├── variables.tf
│ ├── outputs.tf
│ ├── providers.tf
│ └── backend.tf (optional)
│
├── helm/ # Helm chart for the full Vote Application
│ ├── Chart.yaml
│ ├── values.yaml
│ └── templates/
│ ├── vote-deployment.yaml
│ ├── vote-service.yaml
│ ├── result-deployment.yaml
│ ├── result-service.yaml
│ ├── redis-deployment.yaml
│ ├── redis-service.yaml
│ ├── ingress.yaml
│ └── secrets.yaml
│
└── manifests/ # Raw Kubernetes YAML (for learning/testing)
├── 01-namespace.yml
├── 02-redis-deployment.yml
├── 03-redis-service.yml
├── 04-app-deployment.yml
├── 05-app-service.yml
├── 06-result-deployment.yml
├── 07-result-service.yml
└── ingress.yml

yaml
Copy code

---

## 🚀 Deployment Workflow

### **1️⃣ Deploy AKS Using Terraform**

```bash
cd terraform
terraform init
terraform plan
terraform apply -auto-approve
After the cluster is created, fetch kubeconfig:

bash
Copy code
az aks get-credentials -g <resource_group> -n <cluster_name>
2️⃣ Deploy the Application Using Helm
bash
Copy code
cd helm
helm install vote-app .
To update the release:

bash
Copy code
helm upgrade vote-app .
To uninstall:

bash
Copy code
helm uninstall vote-app
3️⃣ (Optional) Deploy Using Raw Kubernetes YAML
Useful for debugging, testing, or manual deployments.

bash
Copy code
kubectl apply -f manifests/
🔐 Secrets & Configuration
All passwords are stored inside Kubernetes Secrets.

Helm supports overriding secrets via CLI:

bash
Copy code
helm install vote-app . \
  --set redis.password=YourPassword \
  --set app.image.tag=v1.0.0
You can also manage secrets manually:

bash
Copy code
kubectl create secret generic vote-secret \
  --from-literal=redis_password=YourPassword
🌐 Ingress & Public Access
After deployment, get the ingress IP:

bash
Copy code
kubectl get ingress
Add a DNS record or hit the public IP directly.

📊 Monitoring & Scaling
Kubernetes HPA example:

bash
Copy code
kubectl autoscale deployment vote-app --min=1 --max=5 --cpu-percent=60
🧪 Testing the Application
Check all running pods:

bash
Copy code
kubectl get pods -A
Test the service:

bash
Copy code
kubectl port-forward svc/vote-service 8080:80
Open:

arduino
Copy code
http://localhost:8080
🧹 Cleanup
Remove Helm resources:

bash
Copy code
helm uninstall vote-app
Remove the cluster:

bash
Copy code
cd terraform
terraform destroy
👨‍💻 Author
Ahmed Hessn
DevOps Engineer
