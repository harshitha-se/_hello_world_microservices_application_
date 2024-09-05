# Microservices Assignment

## Objective
The goal of this assignment is to demonstrate the implementation of a microservices architecture. You will create a simple "Hello World" microservices application using Spring Boot, Docker, and Kubernetes. The application consists of two microservices that communicate to return the combined message "Hello World".

## Services Overview

### Hello Service
- **Endpoint**: `/hello`
- **Response**: `"Hello"`

### World Service
- **Endpoint**: `/world`
- **Response**: `"World"`

## Development Setup

### Prerequisites
- Java Development Kit (JDK) 17
- Maven
- Docker
- Kubernetes (Minikube or another Kubernetes cluster)

### Building and Running Locally

1. **Clone the Repository**
   ```bash
   git clone <your-repo-url>
   cd <your-repo-directory>
2. **Build and Run the Hello Service**
   ```bash
   cd hello-service
   mvn clean package
   java -jar target/hello-service.jar
3. **Build and Run the World Service**
      ```bash
   cd ../world-service
   mvn clean package
   java -jar target/world-service.jar
4. **Access the Services (Manual Boot-Up)**
- **Hello Service**: `http://localhost:8080/hello`
- **World Service**: `http://localhost:8081/world`

## Containerization with Docker
### Build and Push Docker Images

1. **Build and Push Hello Service (Using Dockerfile)**
   ```bash
   docker login
   cd hello-service
   docker build -t dockerneel2k/hello-service:1.0 .
   docker push dockerneel2k/hello-service:1.0

2. **Build and Push World Service (Using Dockerfile)**
   ```bash
   cd ../world-service
   docker build -t dockerneel2k/world-service:1.0 .
   docker push dockerneel2k/world-service:1.0

3. **Running the Docker Containers and prompting back to terminal**
   ```bash
   docker run -d -p 8080:8080 --name hello-service dockerneel2k/hello-service:1.0
   docker run -d -p 8081:8081 --name world-service dockerneel2k/world-service:1.0
4. **Access the Services**
- **Hello Service**: `http://localhost:8080/hello`
- **World Service**: `http://localhost:8081/world`


### Docker Image Links

- **Hello Service**: `https://hub.docker.com/repository/docker/dockerneel2k/hello-service/general`
- **World Service**: `https://hub.docker.com/repository/docker/dockerneel2k/world-service/general`

## Kubernetes Deployment
### Deployment and Service Configuration

1. **Hello Service Deployment**
   ```yaml
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: hello-deployment
   spec:
     replicas: 1
     selector:
       matchLabels:
         app: hello
     template:
       metadata:
         labels:
           app: hello
       spec:
         containers:
           - name: hello
             image: dockerneel2k/hello-service:1.0
             ports:
               - containerPort: 8080

2. **World Service Deployment**
   ```yaml
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: world-deployment
   spec:
     replicas: 1
     selector:
       matchLabels:
         app: world
     template:
       metadata:
         labels:
           app: world
       spec:
         containers:
           - name: world
             image: dockerneel2k/world-service:1.0
             ports:
               - containerPort: 8081
   
3. **Services**
   ```yaml
   apiVersion: v1
   kind: Service
   metadata:
     name: hello-service
   spec:
     selector:
       app: hello
     ports:
       - protocol: TCP
         port: 8080
         targetPort: 8080
         nodePort: 30001
     type: NodePort

   ---
   apiVersion: v1
   kind: Service
   metadata:
     name: world-service
   spec:
     selector:
       app: world
     ports:
       - protocol: TCP
         port: 8081
         targetPort: 8081
         nodePort: 30002
     type: NodePort

### Deploy to Kubernetes

1. **Apply Deployments and Services**
   ```bash
   kubectl apply -f hello-deployment.yaml
   kubectl apply -f world-deployment.yaml
   kubectl apply -f services.yaml
   
2. **Verify Deployment**
   ```bash
   kubectl get deployments
   kubectl get services

### Access the Services
- **Hello Service**: `http://<minikube-ip>:30001/hello`
- **World Service**: `http://<minikube-ip>:30002/world`

## Testing and Integration
### Combined Service Test
1. **Create a Script to Test Combined Services**
   ```bash
   # test-combined.sh
   curl http://localhost:8080/hello && curl http://localhost:8081/world
2. **Run the Script**
   ```bash
   chmod +x test-combined.sh
   ./test-combined.sh

## Troubleshooting
- **ImagePullBackOff**: Ensure that your Docker images are correctly pushed to Docker Hub and that the image names and tags in your Kubernetes deployment YAML files match those in Docker Hub.
- **Access Issues**: Verify that the Kubernetes services are correctly configured with the right NodePorts and that Minikube is running.
### Note
- All the dockers and manifest's are inside the specific project folders.



