# Session 8: Docker Networking & Volume Homework

---

## 👤 Student Information
* **Name:** Nachiketas Iyer
* **Enrollment Number:** 24bcs10131
* **Repository:** [devops-heros](https://github.com/Nachiket115/devops-heros)
* **Branch:** `session8-docker-networking-volume`

---

## 📖 Overview
This repository contains the completed tasks for **Session 8: Docker Networking & Volumes**, including:
1. **Container Networking & Isolation (3 Networks, 3-Tier Architecture)**
2. **Host Network Mode with Apache2**
3. **Bind Mounts with Live Real-Time Webpage Updates**
4. **Comprehensive Research on Docker Overlay Networks**

---

## 🌐 Task 1: Docker Container Networking

### 🎯 Objective:
Create a 3-tier container architecture (`frontend`, `backend`, `database`) across 3 distinct Docker networks, demonstrating network isolation and secure inter-container communication.

### 🏗️ Network Architecture:
* **`frontend-net`**: Connects `frontend-container` and `backend-container`.
* **`backend-net`**: Connects `backend-container` and `database-container`.
* **`monitoring-net`**: 3rd isolated network for management/monitoring.
* **Security Principle:** The frontend can communicate with the backend, and the backend can communicate with the database. However, the frontend is strictly **isolated** and cannot reach the database directly.

```
+-----------------------------------------------------------------------------------+
|                                                                                   |
|    +--------------------+       [frontend-net]       +--------------------+       |
|    | frontend-container | <========================> | backend-container  |       |
|    |      (Alpine)      |                            |      (Alpine)      |       |
|    +--------------------+                            +--------------------+       |
|              |                                                 |                  |
|              |                                                 | [backend-net]    |
|              x (NO DIRECT ROUTE - ISOLATED)                    |                  |
|              |                                                 v                  |
|              + - - - - - - - - - - - - - - - - - - - > +--------------------+     |
|                                                        | database-container |     |
|                                                        |    (MySQL 8.0)     |     |
|                                                        +--------------------+     |
|                                                                                   |
|                                [monitoring-net]                                   |
|                         (3rd administrative network)                              |
+-----------------------------------------------------------------------------------+
```

### 💻 Step-by-Step Commands:

```bash
# 1. Create 3 different Docker networks
docker network create frontend-net
docker network create backend-net
docker network create monitoring-net

# 2. Start Database container (MySQL 8.0 on backend-net)
docker run -d --name database-container --network backend-net -e MYSQL_ROOT_PASSWORD=rootpassword mysql:8.0

# 3. Start Backend container on frontend-net and connect it to backend-net
docker run -d --name backend-container --network frontend-net alpine sleep 3600
docker network connect backend-net backend-container

# 4. Start Frontend container on frontend-net
docker run -d --name frontend-container --network frontend-net alpine sleep 3600
```

### 🧪 Connectivity Verification Tests:

1. **Frontend &rarr; Backend (Connected on `frontend-net`):**
   ```bash
   docker exec frontend-container ping -c 2 backend-container
   ```
   *Result:* ✅ **0% packet loss (SUCCESS)**

2. **Backend &rarr; Database (Connected on `backend-net`):**
   ```bash
   docker exec backend-container ping -c 2 database-container
   ```
   *Result:* ✅ **0% packet loss (SUCCESS)**

3. **Frontend &rarr; Database (Isolation Test):**
   ```bash
   docker exec frontend-container ping -c 2 -W 2 database-container
   ```
   *Result:* 🔒 **`ping: bad address 'database-container'` (ISOLATED - Expected)**

### 📸 Screenshot: Task 1 Container Networking
![Task 1 - Container Networking](screenshots/task1-networking.png)

---

## 🖥️ Task 2: Host Network

### 🎯 Objective:
Pull the Apache2 image from Docker Hub, run a container attached directly to the **Host Network**, and inspect its networking mode.

### 📝 What I Understood:
In **Host Network Mode** (`--network host`), the container does not receive its own isolated network stack or IP address. Instead, it directly binds to the host machine’s network namespace, eliminating Docker bridge overhead and NAT (Network Address Translation).

### 💻 Commands:
```bash
# 1. Pull Apache2 image from Docker Hub
docker pull httpd:alpine

# 2. Run Apache2 container using Host Network
docker run -d --name apache-host --network host httpd:alpine

# 3. Inspect container network mode
docker inspect apache-host --format '{{ .HostConfig.NetworkMode }}'
```

### 📊 Verification Output:
```bash
$ docker inspect apache-host --format '{{ .HostConfig.NetworkMode }}'
host
```

### 📸 Screenshot: Task 2 Host Network
![Task 2 - Host Network](screenshots/task2-host-network.png)

---

## 💾 Task 3: Bind Mount

### 🎯 Objective:
Bind mount a local host folder into an Nginx container, verify the webpage content, modify the HTML file on the host machine, and verify that changes appear immediately **without rebuilding the image or restarting the container**.

### 📝 What I Understood:
A **Bind Mount** creates a direct link between a specific directory on the host filesystem and a path inside the container. This enables rapid live-reloading during development since any changes made by the developer on the host machine are instantaneously accessible to the container.

### 💻 Step-by-Step Commands:

```bash
# 1. Create a local folder and index.html
mkdir -p bind-mount-demo/html
echo "<!DOCTYPE html><html><head><title>Bind Mount Demo - Initial</title></head><body><h1>Hello students</h1><p>Initial webpage served from local host folder via Docker Bind Mount.</p></body></html>" > bind-mount-demo/html/index.html

# 2. Run Nginx container with Bind Mount on Port 8089
docker run -d --name nginx-bindmount -p 8089:80 -v $(pwd)/bind-mount-demo/html:/usr/share/nginx/html nginx:alpine

# 3. Access the webpage to verify initial content
curl http://localhost:8089
```

### 📸 Screenshot: Initial Webpage (`Hello students`)
![Task 3 - Bind Mount Initial](screenshots/task3-bind-mount-initial.png)

---

### 🔄 Live File Modification (Without Container Restart):

```bash
# 4. Modify the index.html file on host machine
echo "<!DOCTYPE html><html><head><title>Bind Mount Demo - Updated</title></head><body><h1>Hello students</h1><p><b>Live update verified!</b> Changes reflected instantaneously without restarting the container.</p></body></html>" > bind-mount-demo/html/index.html

# 5. Refresh browser or curl to verify live reflected update
curl http://localhost:8089
```

### 📸 Screenshot: Live Updated Webpage
![Task 3 - Bind Mount Updated](screenshots/task3-bind-mount-updated.png)

---

## 🌐 Task 4: Research on Docker Overlay Networks

### 1. What is an Overlay Network?
An **Overlay Network** is a distributed virtual network driver in Docker that connects containers across **multiple physical or virtual Docker host machines (nodes)**. It creates an abstracted, flat Layer 2 network over an existing Layer 3 network infrastructure, allowing containers on different servers to communicate securely and directly by container name/IP without requiring host-level port mappings.

```
       Docker Host 1 (Worker 1)                 Docker Host 2 (Worker 2)
+------------------------------------+   +------------------------------------+
|  +------------------------------+  |   |  +------------------------------+  |
|  |   Frontend Container         |  |   |  |   Backend Container          |  |
|  |   IP: 10.0.0.2               |  |   |  |   IP: 10.0.0.3               |  |
|  +--------------+---------------+  |   |  +--------------+---------------+  |
|                 |                  |   |                 |                  |
|                 v                  |   |                 v                  |
|        [ VXLAN Overlay ]           |   |        [ VXLAN Overlay ]           |
+-----------------+------------------+   +-----------------+------------------+
                  |                                        |
                  +============== [ UDP 4789 ] ============+
                       Underlying Cloud / Physical LAN
```

---

### 2. How Overlay Networks Work Across Multiple Hosts:
* **VXLAN Encapsulation (Data Plane):** Standard container Ethernet frames (Layer 2) are encapsulated inside UDP packets on port **`4789`** and routed across the physical network. The receiving Docker host decapsulates the packet and delivers it directly to the target container.
* **Gossip Protocol (Control Plane):** Swarm managers and worker nodes use a decentralized Gossip Protocol (on port **`7946`** TCP/UDP) to exchange container IP-to-host mappings and routing tables automatically.
* **Routing Mesh (Ingress):** Any external request received on a published port on *any* node in the cluster is automatically routed to an active container replica running on *any other* node.
* **Optional IPSec Encryption:** By passing `--opt encrypted`, all inter-host container traffic is encrypted at the kernel level using AES in GCM mode (IP protocol 50 / ESP).

---

### 3. Core Use Cases:
1. **Multi-Host Swarm Clusters:** Running distributed microservices spanning multiple cloud instances or bare-metal servers.
2. **Zero-Trust Network Segmentation:** Creating separate, isolated overlay networks for frontend traffic, backend APIs, and sensitive database tiers across different data centers.
3. **Hybrid & Multi-Cloud Connectivity:** Securely connecting containers distributed across AWS, Azure, GCP, and on-premise infrastructure.

---

### 4. Command Reference:
```bash
# 1. Initialize Docker Swarm cluster
docker swarm init

# 2. Create an attachable Overlay Network
docker network create -d overlay --attachable prod-overlay-net

# 3. Deploy a service on the overlay network
docker service create --name web-service --network prod-overlay-net -p 80:80 nginx:alpine
```

---

## ⚡ Quick Test Script
To re-run and verify all tasks at any time, execute the included runner script:
```bash
chmod +x run_homework.sh
./run_homework.sh
```

---

## 🏁 Submission Checklist
- [x] **Task 1:** Created 3 networks (`frontend-net`, `backend-net`, `monitoring-net`), connected backend to 2 networks, verified ping connectivity & isolation, and included screenshot.
- [x] **Task 2:** Pulled Apache2 image, ran with `--network host`, verified `host` network mode, and included screenshot.
- [x] **Task 3:** Created local folder, bind mounted to Nginx, verified initial `Hello students` webpage, modified file live, verified real-time update without restart, and included screenshots.
- [x] **Task 4:** Researched and documented Docker Overlay Networks, architecture, VXLAN, and use cases.
- [x] **Branch:** Created and updated on dedicated branch `session8-docker-networking-volume`.
