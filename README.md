# 🚀 End-to-End MLOps Project with DagsHub, DVC, MLflow, Docker, AWS ECR, EKS, Prometheus & Grafana

This repository contains a complete end-to-end MLOps pipeline — from project setup to deployment on AWS EKS, along with monitoring using Prometheus & Grafana.

The steps below document the *entire flow*, making it easy to reproduce or onboard new contributors.

---

# 📁 1. Project Initialization & Structure Setup

### **1. Repository & Environment Setup**

* Create GitHub repository and clone locally
* Create virtual environment

  ```bash
  conda create -n atlas python=3.10
  conda activate atlas
  ```
* Install cookiecutter

  ```bash
  pip install cookiecutter
  cookiecutter -c v1 https://github.com/drivendata/cookiecutter-data-science
  ```
* Rename folder: `src.models → src.model`
* Commit and push:

  ```bash
  git add .
  git commit -m "Initial project structure"
  git push
  ```

---

# 🧪 2. MLflow Tracking with DagsHub

### **2.1 Connect GitHub Repo to DagsHub**

* Visit [https://dagshub.com/dashboard](https://dagshub.com/dashboard)
* Create → New Repo → Connect GitHub repo
* Copy experiment tracking URL & MLflow code snippet
* Install dependencies

  ```bash
  pip install dagshub mlflow
  ```

### **2.2 Run Experiments**

* Execute experiment notebooks
* Commit results

  ```bash
  git add .
  git commit -m "Experiment results"
  git push
  ```

---

# 📦 3. DVC Setup for Pipeline & Data Versioning

### **3.1 Initialize DVC**

```bash
dvc init
```

### **3.2 Create Local Dummy Storage**

```bash
mkdir local_s3
dvc remote add -d mylocal local_s3
```

### **3.3 Add Source Code Modules**

Inside `src/`, add:

* logger
* data_ingestion.py
* data_preprocessing.py
* feature_engineering.py
* model_building.py
* model_evaluation.py
* register_model.py

### **3.4 Add Pipeline & Parameters**

* `dvc.yaml`
* `params.yaml`

### **3.5 Run Pipeline**

```bash
dvc repro
dvc status
git add .
git commit -m "Added DVC pipeline"
git push
```

---

# ☁️ 4. Configure DVC Remote Storage on AWS S3

### **4.1 Create AWS IAM + S3**

* IAM user
* S3 bucket
* Save credentials

### **4.2 Install Required Tools**

```bash
pip install dvc[s3] awscli
aws configure
```

### **4.3 Add S3 as Remote**

```bash
dvc remote add -d myremote s3://<bucket-name>
```

---

# 🌐 5. Flask App Integration

### **5.1 Create Flask App Directory**

```
flask_app/
```

### **5.2 Install Flask**

```bash
pip install flask
```

### **5.3 Push DVC data to S3**

```bash
dvc push
```

### **5.4 Export Dependencies**

```bash
pip freeze > requirements.txt
```

### **5.5 Add CI/CD Workflow**

Add `.github/workflows/ci.yaml`

### **5.6 Generate DagsHub Token**

Add to GitHub → Settings → Secrets & Variables

---

# 🧪 6. Add Test Scripts

Create:

```
tests/
scripts/
```

---

# 🐳 7. Dockerization

### **7.1 Auto-generate requirements for Flask**

```bash
pip install pipreqs
cd flask_app
pipreqs . --force
```

### **7.2 Build Docker Image**

```bash
docker build -t capstone-app:latest .
```

### **7.3 Run Container**

```bash
docker run -p 8888:5000 -e CAPSTONE_TEST=<token> capstone-app:latest
```

### **7.4 (Optional) Push to DockerHub**

```bash
docker push <user>/capstone-app:latest
```

---

# 🔐 8. AWS Setup for CI/CD → ECR Deployment

Add environment variables in GitHub Secrets:

* AWS_ACCESS_KEY_ID
* AWS_SECRET_ACCESS_KEY
* AWS_REGION
* AWS_ACCOUNT_ID
* ECR_REPOSITORY

---

# ⛓ 9. Fix Windows AWS CLI Conflicts

### Remove python-based AWS CLI

```bash
pip uninstall awscli
```

### Ensure proper AWS CLI path

`C:\Program Files\Amazon\AWSCLIV2\`

---

# ☸️ 10. Install Kubernetes Tools

### Install kubectl

Download & move to System32.

### Install eksctl

Download, unzip, move to System32.

Verify tools:

```bash
aws --version
kubectl version --client
eksctl version
```

---

# 🌲 11. Create AWS EKS Cluster

```bash
eksctl create cluster \
 --name flask-app-cluster \
 --region us-east-1 \
 --nodegroup-name flask-app-nodes \
 --node-type t3.small \
 --nodes 1
```

Update kubeconfig:

```bash
aws eks --region us-east-1 update-kubeconfig --name flask-app-cluster
```

Verify:

```bash
kubectl get nodes
kubectl get namespaces
```

---

# 🚀 12. Deploy App to EKS via CI/CD

Update:

* `ci.yaml`
* `deployment.yaml`
* Dockerfile

Also update security group to allow inbound **5000 port**.

### Get LoadBalancer external IP:

```bash
kubectl get svc flask-app-service
```

Test:

```bash
curl http://<external-ip>:5000
```


# ✅ Completed MLOps Architecture Includes

* **Cookiecutter project structure**
* **MLflow experiment tracking on DagsHub**
* **DVC data & pipeline versioning**
* **Flask API for model inference**
* **Docker containerization**
* **CI/CD using GitHub Actions**
* **AWS ECR image storage**
* **AWS EKS Kubernetes deployment**

---

