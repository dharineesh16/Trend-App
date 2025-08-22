
# 📘 Project Deployment Guide – Trend/Brain Tasks App  

## 📌 Overview  
This project demonstrates how to deploy a **React application** to **AWS EKS (Kubernetes)** using:  
- **Docker** (containerization)  
- **Amazon ECR** (image registry)  
- **Amazon EKS** (Kubernetes cluster)  
- **Jenkins Pipeline / GitHub Actions** (CI/CD automation)  
- **Prometheus + Grafana** (monitoring and visualization)  

The application is exposed using a **Kubernetes LoadBalancer**, making it accessible from the internet.  

---

## 🛠️ Prerequisites  

- AWS Account (with **EKS**, **ECR**, **EC2**, **IAM**, **VPC** permissions)  
- `kubectl` installed and configured  
- `awscli` installed and configured  
- Docker installed locally  
- Jenkins / CI pipeline configured  

---

## ⚙️ Setup Instructions  

### 1️⃣ Clone the Repository  
```bash
git clone https://github.com/<your-username>/<your-repo>.git
cd <your-repo>
```

### 2️⃣ Dockerize the Application  
Build & test locally:  
bash
docker build -t trend-app:latest .
docker run -p 3000:3000 trend-app:latest
### 3️⃣ Push Docker Image to ECR  
bash
aws ecr create-repository --repository-name trend-app
aws ecr get-login-password --region ap-south-1 | docker login --username AWS --password-stdin <AWS_ACCOUNT_ID>.dkr.ecr.ap-south-1.amazonaws.com
docker tag trend-app:latest <AWS_ACCOUNT_ID>.dkr.ecr.ap-south-1.amazonaws.com/trend-app:latest
docker push <AWS_ACCOUNT_ID>.dkr.ecr.ap-south-1.amazonaws.com/trend-app:latest

### 4️⃣ Deploy on EKS  

#### Deployment YAML (`deployment.yaml`)
Apply:  
bash
kubectl apply -f deployment.yaml
kubectl apply -f trend-service.yaml
kubectl get svc
✅ Note the **LoadBalancer DNS / ARN** from the service output.  



### 5️⃣ Jenkins CI/CD Pipeline  
jenkins url:http://13.127.41.61:8080/


## 📊 Monitoring with Prometheus & Grafana  

1. Install Prometheus & Grafana via Helm:  
   ```bash
   helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
   helm install prometheus prometheus-community/kube-prometheus-stack -n monitoring --create-namespace
   ```

2. Get Grafana Admin Password:  
   ```bash
   kubectl get secret prometheus-grafana -n monitoring -o jsonpath="{.data.admin-password}" | base64 --decode ; echo


3. Access Grafana via LoadBalancer:  
   bash
   kubectl get svc -n monitoring
   - Open Grafana `http://<EXTERNAL-IP>:80`  
   - Login with `admin / <password>`  

4. Add Prometheus Data Source:  
   - URL: `http://prometheus-kube-prometheus-prometheus.monitoring.svc.cluster.local:9090`  
## 📸 Screenshots (Attached Here)  
- ✅ terraform output
 https://github.com/dharineesh16/Trend-App/blob/main/screenshots/1.png
- ✅ LoadBalancer DNS working  
-  https://github.com/dharineesh16/Trend-App/blob/main/screenshots/13.png
- ✅ Jenkins pipeline run
  https://github.com/dharineesh16/Trend-App/blob/main/screenshots/6.png
- ✅ prometheus
  https://github.com/dharineesh16/Trend-App/blob/main/screenshots/10.png
- ✅ Grafana Dashboard
   https://github.com/dharineesh16/Trend-App/blob/main/screenshots/8.png


---

## 🔗 LoadBalancer ARN  

📍 Application is exposed via:

**LoadBalancer ARN / DNS:**  
a2e4c6747ac634d36836fd2009aceb49-1560738099.ap-south-1.elb.amazonaws.com
## ✅ Conclusion  
This project demonstrates an **end-to-end CI/CD pipeline** with monitoring, making the app production-ready on AWS EKS.  
 
