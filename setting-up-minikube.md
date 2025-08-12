# Update and upgrade the ubuntu machine.
sudo apt update && upgrade -y

# Installing docker.
sudo apt install docker.io -y
# Add user to group docker.
sudo usermod -aG docker $USER
# Start a new shell session with the updated group membership (Apply group change).
newgrp docker

# Downloading and installing kubctl.
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl

# Downloading and installing Minikube.
curl -LO https://github.com/kubernetes/minikube/releases/latest/download/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube && rm minikube-linux-amd64
# Start minikube
minikube start

# Create deployment for a node js app.
vi deployment.yaml

apiVersion: apps/v1
kind: Deployment
metadata:
  name: todo-app
spec:
  replicas: 2
  selector:
    matchLabels:
      app: todo-app
  template:
    metadata:
      labels:
        app: todo-app
    spec:
      containers:
      - name: todo-app
        image: gauravkumar05/todo-app:latest
        ports:
        - containerPort: 3000

# Apply deployment
kubectl apply -f deployment.yaml

# Expose the app via Service.yaml

vi service.yaml

apiVersion: v1
kind: Service
metadata:
  name: todo-service
spec:
  type: NodePort
  selector:
    app: todo-app
  ports:
    - protocol: TCP
      port: 3000
      targetPort: 3000
      nodePort: 30007

# Apply Service.yaml
kubectl apply -f service.yaml

# Scale to 4 replicas
kubectl scale deployment todo-app --replicas=4

# Describe deployment:
kubectl describe deployment todo-app

# View logs for a pod:
kubectl logs <pod-name>

# Access the App
minikube service todo-service



