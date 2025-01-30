Day1:-
1)How to set up Kubernetes using K3s on an AWS Spot Instance and deploy an Nginx webpage in the Kubernetes cluster?

Here’s a detailed, step-by-step guide on how to set up Kubernetes using K3s on an AWS Ubuntu Spot Instance and deploy an Nginx webpage in the Kubernetes cluster.

Steps
1. Launch an AWS Ubuntu Spot Instance
Log in to AWS Management Console.

Navigate to the EC2 Dashboard → Spot Requests.

Create a Spot Instance:

AMI: Ubuntu 22.04 or later

Instance Type: t2.medium (or any preferred type)

Key Pair: Create new one or select an existing one.

Security Group: Allow the following:

SSH (port 22)
HTTP (port 80)
HTTPS (port 443)
Kubernetes NodePort range (30000–32767)
Launch the Ubuntu instance and connect via SSH:

By using below command we can connect to the instance.

ssh -i <your-key.pem> ubuntu@<your-instance-public-ip>
2. Install K3s on the Ubuntu Spot Instance
Update the system Packages:

sudo apt update && sudo apt upgrade -y
Install Prerequisites curl and wget :

sudo apt install curl wget -y
Install K3s (lightweight Kubernetes):

curl -sfL https://get.k3s.io | sh -
Verify K3s installation:

kubectl version
kubectl get nodes
You should see the node in a Ready state.

3. Deploy Nginx in Kubernetes
Create a Deployment for Nginx: Save the YAML as nginx-deployment.yaml:

apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:latest
        ports:
        - containerPort: 80
By using below command we can create the Deployment:

kubectl apply -f nginx-deployment.yaml
Example Output

image

Expose Nginx using a NodePort Service: Save the YAML as nginx-service.yaml:

apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  type: NodePort
  selector:
    app: nginx
  ports:
  - port: 80
    targetPort: 80
    nodePort: 30007
By using below command we can create the Service:

kubectl apply -f nginx-service.yaml
Example Output

Screenshot 2025-01-29 173135

4. Verify Nginx Deployment
Check the Deployment and Pods and Service:

kubectl get deployments
kubectl get pods
kubectl get svc
Example Output:-

Screenshot 2025-01-29 173430

Now we can access Nginx in browser:

Open the URL http://<public-ip>:30007 in a web browser. You should see the default Nginx welcome page.

Example Output:-

Screenshot 2025-01-29 173608

5. To Delete Resources
To delete the Deployment and Service:

kubectl delete -f nginx-deployment.yaml
kubectl delete -f nginx-service.yaml
Example Output:-

Screenshot 2025-01-29 173936 Screenshot 2025-01-29 173952

Conclusion
Successfully set up Kubernetes using K3s on an AWS Ubuntu Spot Instance and deployed an Nginx webpage in the Kubernetes cluster.

