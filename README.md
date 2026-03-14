
# Kubernetes Ingress Deployment

This repository demonstrates how to **build a Docker image, push it to AWS ECR, and deploy an application to Kubernetes using Deployment, Service, and Ingress**.

---

# Project Workflow

Developer → Docker Build → Push Image to AWS ECR → Kubernetes Deployment → Service → Ingress → External Access

---

# Project Structure

```
kubernetes-ingress-deployment
│
├── Monolithic/       # Monolithic application example
├── aws/              # AWS related configuration
├── azure/            # Azure examples
├── gcp/              # GCP examples
├── k8s-files/        # Kubernetes YAML files
│
├── Dockerfile        # Docker image build configuration
├── index.html        # Sample web application
└── README.md
```

---

Step 1: Build Docker Image

```
# Install Docker
yum install docker -y
systemctl start docker

# Build Docker Image (Main Application)
docker build -t main .   # root folder contains index.html and Dockerfile

# Build AWS Image
cd aws/
docker build -t aws .

# Build Azure Image
cd ../azure/
docker build -t azure .

# Build GCP Image
cd ../gcp/
docker build -t gcp .
```

Step 2: Tag Image for AWS ECR

Example:

```
docker tag main:latest 123456789012.dkr.ecr.us-east-1.amazonaws.com/main:latest
```


Step 3: Login to AWS ECR

```
aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin <aws_account_id>.dkr.ecr.us-east-1.amazonaws.com
```


Step 4: Push Image to ECR

```
docker push 123456789012.dkr.ecr.us-east-1.amazonaws.com/main:latest
```


Step 5: Deploy Application to Kubernetes

Deployment uses the image stored in **AWS ECR**.

Example snippet:

```
containers:
- name: nginx
  image: <aws_account_id>.dkr.ecr.us-east-1.amazonaws.com/nginx-hpa:latest
  ports:
  - containerPort: 80
```

Apply deployment:

```
kubectl apply -f k8s-files/deployment.yaml
```


Step 6: Create Kubernetes Service

```
kubectl apply -f k8s-files/service.yaml
```


Step 7: Install NGINX Ingress Controller

```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.2.1/deploy/static/provider/cloud/deploy.yaml
```


Step 8: Deploy Ingress

```
kubectl apply -f k8s-files/ingress.yaml
```


# Verify Deployment

```
kubectl get pods
kubectl get svc
kubectl get ingress
```

---

# Application Traffic Flow

Internet → Ingress Controller → Service → Pods

---
