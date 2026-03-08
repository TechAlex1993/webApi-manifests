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

from flask import Flask, jsonify
import os
import socket
import datetime

app = Flask(__name__)

@app.route('/')
def hello():
    return jsonify({
        "message": "Hello, World!",
        "hostname": socket.gethostname(),
        "timestamp": datetime.datetime.now().isoformat(),
        "version": os.environ.get('APP_VERSION', '1.0.0')
    })

@app.route('/health')
def health():
    return jsonify({"status": "healthy"}), 200

@app.route('/ready')
def ready():
    return jsonify({"status": "ready"}), 200

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)




