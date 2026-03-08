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
