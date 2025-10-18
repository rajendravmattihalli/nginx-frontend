# nginx-frontend
nginx-frontedn web application


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




