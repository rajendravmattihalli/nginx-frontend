# nginx-frontend
nginx-frontend web application

<ins> **flaskapi-backend application - contents** </ins>

> 1. Multistage Docker build
> 2. Observability
> 3. Kubernetes manifest 
> 4. CI Integration
> 5. ArgoCD Integartion
> 6. Sandbox Testing

## Docker Build - Multistage

Folder structure
```
├── docker-multistage
│   ├── Dockerfile
│   ├── index.html
│   ├── nginx.conf
│   └── server.conf
```
**Prerequisite**
> Docker

**Manual Build**
> docker build -t nginx-frontend:latest 

**Manual deploy**
> docker run --name nginx-frontend -d -p 80:80 nginx-frontend:latest

# Observability
**Prometheus Instrumentation**
<ins> **RED Metrics** </ins>
1. Request count
2. Error count
3. Duration Latency

<ins> **nginx-instrumentation steps** </ins>
1. nginx server configured to emit metrics for intsturmentation - server.conf
2. nginx-exporter side car container deployment for scraping nginx instrument metrics
   
ScrapeEndpoint = /metrics 
ScrapeEndpointPort = 80


<ins> **Prometheus Operator Installation** </ins>
**Prerequisite**
1. Kubernetes
2. Helm
3. Kubectl

## CI Integration


## ArgoCD integration
flaskapi-backend app ArgoCD integration steps listed below.

Folder structure 
```
.
├── argo-cd
│   ├── application.yaml
│   └── project.yaml
├── kubernetes
│   ├── namespace.yml
│   ├── nginx-frontend-deployment.yml
│   └── nginx-frontend-service.yml
└── README.md
```

<ins> **Steps for ArgoCD integration** </ins>
**Prerequisite**
> 1. Kubernetes cluster setup running (minikube or cloud)
> 2. ArgoCD installation - Follow steps below 1 to 5 for setup

**MiniKube Setup**
1. create a namespace - argocd
> minikube kubectl -- create namespace argocd
2. create a arogcd deployment 
> minikube kubectl -- apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
3. validate if the pods running in argocd namespace
> minikube kubectl -- get pods -n argocd
4. Acess the argocd admin page on host box
> minikube kubectl -- port-forward service/argocd-server -n argocd 8080:443
5. Fetch the password for web login
> minikube kubectl -- -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo
6. Create - Repository - nginx-frontend using admin page - repository url = https://github.com/rajendravmattihalli/nginx-frontend.git 
> <img width="1292" height="285" alt="Screenshot 2025-10-18 at 5 13 16 PM" src="https://github.com/user-attachments/assets/c07cbf33-723c-4fde-9961-beceb9619f4f" />
8. Create - Project - nginx-frontend
> minikube kubectl -- apply -f argo-cd/project.yaml
9. Create - Application - nginx-frontend
> minikube kubectl -- apply -f argo-cd/application.yaml
10. Final validate the sync status - under application
> 




