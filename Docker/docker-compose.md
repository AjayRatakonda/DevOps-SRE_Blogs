### What is Docker Compose?  
Docker Compose is a tool used to define and run multi-container Docker applications. Instead of running multiple `docker run` commands, you can define all your containers, networks, and volumes in a single **YAML file** (`docker-compose.yml`) and start them with a single command.  

It simplifies container management, especially when working with multiple services like databases, APIs, and frontends.  

---

### Key Concepts in Docker Compose:
1. **Services**: Define containers in `docker-compose.yml`. Each service runs a container.
2. **Networks**: Allows communication between services.
3. **Volumes**: Used for persistent storage.
4. **Environment Variables**: Used for configuration.

---

### How to Practice Docker Compose in Ubuntu 22  

#### **Step 1: Create a Docker Compose YAML file**  
Create a directory and navigate to it:  
```bash
mkdir my-compose-app && cd my-compose-app
```
Create a `docker-compose.yml` file:  
```bash
nano docker-compose.yml
```

Add the following simple example for running **Nginx**:  
```yaml
version: "3.8"

services:
  web:
    image: nginx:latest
    ports:
      - "8080:80"
```
Save and exit the file.

---

#### **Step 2: Start the Container**  
Run the following command to start the container:  
```bash
docker-compose up -d
```
- `up`: Starts the containers.
- `-d`: Runs in detached mode (in the background).

---

#### **Step 3: Verify Running Containers**  
Check if the container is running:  
```bash
docker ps
```

---

#### **Step 4: Access the Application**  
Open a browser and go to:  
```bash
http://your-ec2-public-ip:8080
```
You should see the **Nginx default page**.

---

#### **Step 5: Stop and Remove Containers**  
To stop the running services:  
```bash
docker-compose down
```

---

### Next Steps  
- Modify the `docker-compose.yml` to add **MySQL, Redis, or a custom application**.
- Use **volumes** to persist data.
- Set **environment variables** inside the file.

Would you like a hands-on example with a database or a full-stack application?
