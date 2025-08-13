# POC: ArgoCD

In this Proof of Concept (POC), we will demonstrate the ability to use **ArgoCD** as a Continuous Delivery (CD) tool.

The delivery process will use the **pull model**.  
In our POC, we will follow a **"one application – one cluster"** approach, meaning each application will be deployed to its own dedicated cluster.

---

## Installation Procedure

```bash
# Create cluster
k3d cluster create argo

# Create namespace for ArgoCD
kubectl create namespace argocd

# Install ArgoCD
kubectl apply -n argocd \
  -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

# Port-forward ArgoCD UI
kubectl port-forward svc/argocd-server -n argocd 8080:443 &

# Get the encoded password
kubectl -n argocd get secret argocd-initial-admin-secret \
  -o jsonpath="{.data.password}"

# Decode it
kubectl -n argocd get secret argocd-initial-admin-secret \
  -o jsonpath="{.data.password}" | base64 -d; echo


[![asciicast](https://asciinema.org/a/y3OBD47uK0y7fMPLeFa5weWpO.svg)](https://asciinema.org/a/y3OBD47uK0y7fMPLeFa5weWpO)

###
login to via browser https://127.0.0.1:8080 (accept not secure connection, for production systems install a proper TLS certificate)

###
Press + NEW APP
Application name: demo
Project name: default
Sync Policy: Manual

SOURCE:
Type: GIT 
Repository URL: https://github.com/dkedrovskyi/go-demo-app
Path: Helm 

DESTINATION:
Cluster Url: https://kubernetes.default.svc
Namespace: demo 

AUTO-CREATE NAMESPACE should be enabled 

Press CREATE

###

To Synchonize press SYNC - SYNCHRONIZE

###
On the next step 
We change some configuration in 
https://github.com/dkedrovskyi/go-demo-app/blob/main/helm/values.yaml

NodePort/LoadBalancer

And After Sync We will see that svc type for ambassador changed from NodePort to LoadBalancer

