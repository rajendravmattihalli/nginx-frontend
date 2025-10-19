# nginx-frontend
nginx-frontend web application

<img width="1668" height="277" alt="Screenshot 2025-10-19 at 1 04 31 PM" src="https://github.com/user-attachments/assets/75aab803-1cf6-48b0-bfaa-920175a84929" />


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
   
> ScrapeEndpoint = /metrics , ScrapeEndpointPort = 80

<img width="1680" height="906" alt="Screenshot 2025-10-19 at 1 04 46 PM" src="https://github.com/user-attachments/assets/6b193ef9-6853-4064-8a71-9a6e90539157" />


<ins> **Prometheus Operator Installation** </ins>
**Prerequisite**
1. Kubernetes
2. Helm
3. Kubectl


**service-monitor nginx-exporter**
kubectl apply -f flaskapi-backend-servicemoinitor.yml -n webapp

<img width="1676" height="364" alt="Screenshot 2025-10-19 at 1 59 33 AM" src="https://github.com/user-attachments/assets/c42179e9-7caa-4b6a-9df8-931b4e5a177e" />



**Logging Instrumentation**
1. INFO and ERROR Log
2. stdout/stderr - container
3. Json format

```
nginx-frontend % minikube kubectl -- logs nginx-frontend-78595664b7-bvtk6 -n webapp 
Defaulted container "nginx-frontend" out of: nginx-frontend, nginx-prometheus-exporter
{"time_local":"19/Oct/2025:15:33:23 +0800","remote_addr":"10.244.0.1","request":"GET / HTTP/1.1","status":200,"body_bytes_sent":1044,"request_time":0.000","http_user_agent":"Mozilla/5.0 (Macintosh; Intel Mac OS X 10.15; rv:144.0) Gecko/20100101 Firefox/144.0"}
{"time_local":"19/Oct/2025:15:33:23 +0800","remote_addr":"10.244.0.1","request":"GET /api HTTP/1.1","status":200,"body_bytes_sent":81,"request_time":0.020","http_user_agent":"Mozilla/5.0 (Macintosh; Intel Mac OS X 10.15; rv:144.0) Gecko/20100101 Firefox/144.0"}
{"time_local":"19/Oct/2025:15:33:23 +0800","remote_addr":"10.244.0.1","request":"GET /favicon.ico HTTP/1.1","status":404,"body_bytes_sent":153,"request_time":0.000","http_user_agent":"Mozilla/5.0 (Macintosh; Intel Mac OS X 10.15; rv:144.0) Gecko/20100101 Firefox/144.0"}
2025/10/19 15:33:29 [error] 8#8: *1 access forbidden by rule, client: 10.244.0.1, server: localhost, request: "GET /metrics HTTP/1.1", host: "127.0.0.1:49784"
{"time_local":"19/Oct/2025:15:33:29 +0800","remote_addr":"10.244.0.1","request":"GET /metrics HTTP/1.1","status":403,"body_bytes_sent":153,"request_time":0.000","http_user_agent":"Mozilla/5.0 (Macintosh; Intel Mac OS X 10.15; rv:144.0) Gecko/20100101 Firefox/144.0"}
{"time_local":"19/Oct/2025:15:33:35 +0800","remote_addr":"127.0.0.1","request":"GET /metrics HTTP/1.1","status":200,"body_bytes_sent":97,"request_time":0.000","http_user_agent":"NGINX-Prometheus-Exporter/v1.5.0"}
{"time_local":"19/Oct/2025:15:33:36 +0800","remote_addr":"10.244.0.1","request":"GET /health/metrics HTTP/1.1","status":200,"body_bytes_sent":2374,"request_time":0.080","http_user_agent":"Mozilla/5.0 (Macintosh; Intel Mac OS X 10.15; rv:144.0) Gecko/20100101 Firefox/144.0"}
{"time_local":"19/Oct/2025:15:33:47 +0800","remote_addr":"10.244.0.1","request":"GET /api HTTP/1.1","status":200,"body_bytes_sent":81,"request_time":0.003","http_user_agent":"Mozilla/5.0 (Macintosh; Intel Mac OS X 10.15; rv:144.0) Gecko/20100101 Firefox/144.0"}
{"time_local":"19/Oct/2025:15:34:38 +0800","remote_addr":"127.0.0.1","request":"GET /metrics HTTP/1.1","status":200,"body_bytes_sent":97,"request_time":0.000","http_user_agent":"NGINX-Prometheus-Exporter/v1.5.0"}
{"time_local":"19/Oct/2025:15:34:38 +0800","remote_addr":"10.244.0.1","request":"GET /health/metrics HTTP/1.1","status":200,"body_bytes_sent":2378,"request_time":0.003","http_user_agent":"Mozilla/5.0 (Macintosh; Intel Mac OS X 10.15; rv:144.0) Gecko/20100101 Firefox/144.0"}
```

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
<img width="1675" height="879" alt="Screenshot 2025-10-19 at 1 46 25 PM" src="https://github.com/user-attachments/assets/dd4feb5f-f1ba-4fa9-bd3f-834244fdb672" />





