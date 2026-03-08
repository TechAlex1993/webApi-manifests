# webApi-manifests
CI/CD interview
Local Development Project: Deploy Web API on Minikube with GitOps
1. Project Architecture Diagram
   <img width="605" height="526" alt="image" src="https://github.com/user-attachments/assets/1f352db4-cd17-44d2-8576-ea804f252978" />
   
a) Project Structure


<img width="691" height="506" alt="image" src="https://github.com/user-attachments/assets/2fd3832f-c7da-4ca8-9a94-b7a62aee4951" />

               Step-by-Step Implementation Guide
   Prerequisites Installation
# Install Docker Desktop
# Install Minikube
# Install kubectl
# Install Helm
# Install ArgoCD CLI
# Install Terraform (for structure demo)

 Step 1: Start Minikube
 # Start minikube with sufficient resources
minikube start --cpus=4 --memory=8192 --driver=docker

# Enable ingress addon
minikube addons enable ingress

# Check status
minikube status
kubectl get nodes

Step 2: Create the Web API Application

Create directory structure:

mkdir -p web-api-project/{app-code-repo,helm-chart-repo,manifests-repo,terraform-repo}
cd web-api-project

a)Create the Flask API (app-code-repo/app.py):

Step 3: Create Helm Chart
Create Helm chart structure:

cd helm-chart-repo
helm create web-api-chart
# Remove default templates and create our own
rm -rf web-api-chart/templates/*

a)Create deployment.yaml (helm-chart-repo/web-api-chart/templates/deployment.yaml):

b)Create service.yaml (helm-chart-repo/web-api-chart/templates/service.yaml):

c)Create ingress.yaml (helm-chart-repo/web-api-chart/templates/ingress.yaml):

d)Update Chart.yaml (helm-chart-repo/web-api-chart/Chart.yaml):

e)Update values.yaml (helm-chart-repo/web-api-chart/values.yaml):

Step 4: Setup GitHub Actions CI Pipeline
Create GitHub Actions workflow (app-code-repo/.github/workflows/build-and-push.yml):

Step 5: Setup Manifests Repository

a)Create environment-specific values (manifests-repo/environments/dev/values.yaml):

b)Create ArgoCD application manifest (manifests-repo/environments/dev/application.yaml):

Step 6: Setup Local Docker Registry for Minikube

# Start local registry
docker run -d -p 5000:5000 --name registry registry:2

# Connect Minikube to local registry
minikube addons enable registry

# Or use minikube's built-in registry
minikube addons enable registry-creds

# For loading images directly to minikube
eval $(minikube docker-env)

Step 7: Install and Configure ArgoCD

# Create namespace
kubectl create namespace argocd

# Install ArgoCD
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

# Wait for all pods to be ready
kubectl wait --for=condition=ready pod --all -n argocd --timeout=300s

# Get initial admin password
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d

# Port forward ArgoCD UI
kubectl port-forward svc/argocd-server -n argocd 8080:443

# Login via CLI
argocd login localhost:8080 --username admin --password <get-password-above> --insecure


Step 8: Deploy Application with ArgoCD

# Apply the ArgoCD application manifest
kubectl apply -f manifests-repo/environments/dev/application.yaml

# Check application status
argocd app get web-api-dev

# Sync manually if needed
argocd app sync web-api-dev

# Watch pods
kubectl get pods -w

Step 9: Build and Load Initial Docker Image

# Build the image
cd app-code-repo
docker build -t web-api:latest .

# For Minikube, load the image directly
minikube image load web-api:latest

# Or tag for local registry and push
docker tag web-api:latest localhost:5000/web-api:latest
docker push localhost:5000/web-api:latest

Step 10: Test the Application

# Get the service URL
minikube service web-api-service --url

# Or if using ingress
# Add to /etc/hosts:
echo "$(minikube ip) web-api.local" | sudo tee -a /etc/hosts

# Test the API
curl http://web-api.local



