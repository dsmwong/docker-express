## Setup
[Install Prerequistes](https://cloud.google.com/kubernetes-engine/docs/tutorials/hello-app#option_b_use_command-line_tools_locally)
* Install Cloud SDK
* Install GCloud Kubernetes Component
* Install Docker Desktop (optional)

### Set Project ID
* `export PROJECT_ID=${PROJECTID}`
or
* `--project ${PROJECTID}` for every command
or 
* `gcloud config set project ${PROJECT_ID}`

### GCloud Authentication
* `gcloud auth login` 
* `gcloud auth configure-docker`  <-- allows docker to connect to gcloud

### GCloud Service Permissions
* enable `containerregistry.googleapis.com`
* enable `container.googleapis.com`

### Set Compute Region
* `gcloud config set compute/region ${REGION}`

## Push Image
```zsh
# GKE Sample Project
cd kubernetes-engine-samples/hello-app

# Build Image off Dockerfile
docker build -t gcr.io/${PROJECT_ID}/hello-app:v1 .
docker images

# Test Image Locally
docker run -p 8080:8080 gcr.io/${PROJECT_ID}/hello-app:v1

# Push to Google Container Registry (GCR)
docker push gcr.io/${PROJECT_ID}/hello-app:v1
```

## Create Cluster taken from [here](https://cloud.google.com/kubernetes-engine/docs/tutorials/hello-app#deploying_the_sample_app_to)
```zsh
# Using Autopilot
gcloud container clusters-auto create hello-cluster

# Check nodes created on GKE
kubectl get nodes

# Allow cluster to access container
gcloud container clusters get-credentials hello-cluster

# Create deployment using container image
kubectl create deployment hello-app --image=gcr.io/${PROJECT_ID}/hello-app:v1

# Configure scaling parameters
kubectl scale deployment hello-app --replicas=3
kubectl autoscale deployment hello-app --cpu-percent=80 --min=1 --max=5

# Check the PODs
kubectl get pods

# Expose cluster with Load Balancer
kubectl expose deployment hello-app --name=hello-app-service --type=LoadBalancer --port 80 --target-port 8080

# Check Service
kubectl get service

docker images

# Deletion of LB service
kubectl delete service hello-app-service
```