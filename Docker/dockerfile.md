## What is a Dockerfile?

A Dockerfile is a text document that contains a set of instructions to automate the process of building a Docker image.  

Each instruction in the Dockerfile is executed in sequence to create the final image.  

These instructions include:
- Specifying a base image  
- Installing dependencies  
- Copying files  
- Setting environment variables  
- Defining the command to run the container  
  
### **How to Practice Nginx with a Dockerfile in Ubuntu 22**  
#### **Step 1: Create a Directory for Your Project**  
```sh
mkdir nginx-dockerfile
cd nginx-dockerfile
```

#### **Step 2: Create a Dockerfile**  
Run the following command to create a Dockerfile:  
```sh
vi Dockerfile
```

#### **Step 3: Write the Dockerfile Instructions**  
Copy and paste the following content:  
```dockerfile
# Use Ubuntu as the base image
FROM ubuntu:latest

# Set environment variable
ENV NGINX_PORT=80

# Set the working directory
WORKDIR /app

# Install Nginx
RUN apt update && apt install -y nginx

# Copy an index.html file (optional)
COPY index.html /var/www/html/index.html

# Expose port 80
EXPOSE 80

# Create a volume for logs
VOLUME /var/log/nginx

# Default command to start Nginx
CMD ["nginx", "-g", "daemon off;"]
```

#### **Step 4: Create an index.html File**  
```sh
vi index.html
```
Add some simple content:  
```html
<html>
  <head><title>My Nginx Container</title></head>
  <body><h1>Welcome to Nginx inside Docker!</h1></body>
</html>
```

#### **Step 5: Build the Docker Image**  
```sh
docker build -t nginx:latest .
docker images
```
**Sample Output**

![image](https://github.com/user-attachments/assets/9fb3cdb6-8db7-4d2a-8bb7-940e000f4a0c)
![image](https://github.com/user-attachments/assets/aa398cdf-81ef-4a54-8b31-ad6a4cd2eb93)

#### **Step 6: Run the Nginx Container**  
```sh
docker run -d -p 8080:80 nginx
```
```sh
docker ps
```
**Sample Output**

![image](https://github.com/user-attachments/assets/bb5055d1-d0ff-4503-a7d1-0978e8ea8aab)
![image](https://github.com/user-attachments/assets/13f26e62-50cd-4085-af4d-69c35580b637)

#### **Step 7: Test Nginx**  
Run this command to check if the Nginx server is working:  
```sh
curl http://localhost:8080
```
```sh
http://65.0.21.7:8080/
```
You should see your custom **HTML content** displayed.

**Sample Output**

![image](https://github.com/user-attachments/assets/564ae338-447f-453b-9d11-a10c6c428458)
![image](https://github.com/user-attachments/assets/418ee4a7-2a57-4d29-8be5-11c8138f5baa)


#### **Step 8: Stop and Remove the Container**  
```sh
docker stop <container_id>
docker rm <container_id>
```
**Sample Output**

![image](https://github.com/user-attachments/assets/52d5b478-e902-4397-b34f-45c4c4e93f71)


---
