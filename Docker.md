---

## **Step 6: Executing Commands Inside a Running Container**
Once a container is running, you may need to interact with it.

### **12. Attach to a Running Container**
Attach to the container’s active session:
```sh
docker attach <container_id>
```
**Sample Output**


To detach from the container, use `CTRL + P + Q` without stopping it.

### **13. Execute Commands in a Running Container**
Execute a command inside a running container:
```sh
docker exec -it <container_id> bash
```
Example:
```sh
docker exec -it nginx bash
```
(For Alpine-based images, use `/bin/sh` instead of `bash`.)

---

## **Step 7: Container Logs & Monitoring**
### **14. View Container Logs**
To check logs of a running container:
```sh
docker logs <container_id>
```
To follow live logs:
```sh
docker logs -f <container_id>
```

### **15. Check Container Resource Usage**
Monitor a running container’s resource usage:
```sh
docker stats
```

---

## **Step 8: Container Networking**
### **16. List Networks**
```sh
docker network ls
```
It shows available networks (`bridge`, `host`, `none`, etc.).

### **17. Create a Custom Network**
```sh
docker network create my-network
```

### **18. Run a Container in a Custom Network**
```sh
docker run -d --name web-server --network=my-network nginx
```

### **19. Inspect a Network**
```sh
docker network inspect my-network
```

---

## **Step 9: Docker Volumes & Data Persistence**
By default, when a container is removed, its data is lost. **Volumes** help persist data.

### **20. Create and Use a Volume**
Create a volume:
```sh
docker volume create my-data
```

Run a container with a volume:
```sh
docker run -d -v my-data:/usr/share/nginx/html --name nginx-container nginx
```

### **21. List Volumes**
```sh
docker volume ls
```

### **22. Inspect a Volume**
```sh
docker volume inspect my-data
```

### **23. Remove a Volume**
```sh
docker volume rm my-data
```

---

