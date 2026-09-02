# Docker Multi-Stage Build Homework Submission

---

## 👤 Student Information
* **Name:** Nachiketas Iyer
* **Enrollment Number:** 24bcs10131
* **Repository:** [devops-heros](https://github.com/Nachiket115/devops-heros)
* **Branch:** `session6-docker`

---

## 📌 Task 1: Run Multi-Stage Dockerfile

### 1. Multi-Stage Dockerfile Overview
The multi-stage Dockerfile is located at [`session6-7-docker/multi-stage-dockerfile/Dockerfile`](file:///Users/nachiketr/devops-heros/session6-7-docker/multi-stage-dockerfile/Dockerfile):

```dockerfile
# -------------------------
# Stage 1: Build
# -------------------------
FROM node:20-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .

# -------------------------
# Stage 2: Production
# -------------------------
FROM node:20-alpine AS production
WORKDIR /app
COPY --from=builder /app/package*.json ./
RUN npm install --omit=dev
COPY --from=builder /app/server.js ./
EXPOSE 3000
CMD ["npm", "start"]
```

### 2. Build the Multi-Stage Docker Image
```bash
cd session6-7-docker/multi-stage-dockerfile
docker build -t multistage-app .
```

### 3. Run Container on Port 8080
```bash
docker run -d --name multistage-app -p 8080:3000 multistage-app
```

### 4. Access Application & Verify Output
```bash
$ curl -i http://localhost:8080

HTTP/1.1 200 OK
X-Powered-By: Express
Content-Type: text/html; charset=utf-8
Content-Length: 50
ETag: W/"32-/f6Dub7FU3KvL133TUZVmIWqIxw"
Date: Wed, 02 Sep 2026 10:26:25 GMT
Connection: keep-alive
Keep-Alive: timeout=5

<h1>Hello World from Docker multi-stage build</h1>
```

---

## 📌 Task 2: Documentation & `docker ps` Output

Command output showing the running container on host port **8080**:

```bash
$ docker ps --filter "name=multistage-app"
CONTAINER ID   IMAGE            COMMAND                  CREATED         STATUS         PORTS                                         NAMES
559ec91e3574   multistage-app   "docker-entrypoint.s…"   9 seconds ago   Up 8 seconds   0.0.0.0:8080->3000/tcp, [::]:8080->3000/tcp   multistage-app
```

---

## 📌 Task 3: Docker Application Deployment (Node.js, Python, Java)

Three different types of applications were containerized and deployed alongside the multi-stage build application:

### 1. Node.js Application Deployment
* **Folder:** `session6-7-docker/nodejs-app/`
* **Port Mapping:** `3000:3000`
* **Run Command:** `docker run -d --name nodejs-app -p 3000:3000 nodejs-hello`
* **Verification Output:**
```bash
$ curl -i http://localhost:3000

HTTP/1.1 200 OK
X-Powered-By: Express
Content-Type: text/html; charset=utf-8
Content-Length: 34
Date: Wed, 02 Sep 2026 10:26:28 GMT
Connection: keep-alive

<h1>Hello World from Node.js!</h1>
```

### 2. Python Application Deployment
* **Folder:** `session6-7-docker/python-app/`
* **Port Mapping:** `5001:5000`
* **Run Command:** `docker run -d --name python-app -p 5001:5000 python-hello`
* **Verification Output:**
```bash
$ curl -i http://localhost:5001

HTTP/1.1 200 OK
Server: Werkzeug/3.1.8 Python/3.11.16
Date: Wed, 02 Sep 2026 10:26:30 GMT
Content-Type: text/html; charset=utf-8
Content-Length: 33
Connection: close

<h1>Hello World from Python!</h1>
```

### 3. Java Application Deployment
* **Folder:** `session6-7-docker/java-app/`
* **Port Mapping:** `8082:8080`
* **Run Command:** `docker run -d --name java-app -p 8082:8080 java-hello`
* **Verification Output:**
```bash
$ curl -i http://localhost:8082

HTTP/1.1 200 OK
Date: Wed, 02 Sep 2026 10:26:32 GMT
Content-type: text/html; charset=UTF-8
Content-length: 31

<h1>Hello World from Java!</h1>
```

---

## 📋 Comprehensive `docker ps` Verification Table

```bash
$ docker ps
CONTAINER ID   IMAGE            COMMAND                  CREATED          STATUS          PORTS                                         NAMES
030cf5c73659   java-hello       "/__cacert_entrypoin…"   10 seconds ago   Up 10 seconds   0.0.0.0:8082->8080/tcp, [::]:8082->8080/tcp   java-app
ed8a989c0c1d   python-hello     "python app.py"          12 seconds ago   Up 12 seconds   0.0.0.0:5001->5000/tcp, [::]:5001->5000/tcp   python-app
db065293a620   nodejs-hello     "docker-entrypoint.s…"   15 seconds ago   Up 14 seconds   0.0.0.0:3000->3000/tcp, [::]:3000->3000/tcp   nodejs-app
559ec91e3574   multistage-app   "docker-entrypoint.s…"   18 seconds ago   Up 17 seconds   0.0.0.0:8080->3000/tcp, [::]:8080->3000/tcp   multistage-app
```

---

## 🏁 Submission Summary Checklist

- [x] **Task 1:** Cloned/navigated to repository with multi-stage Dockerfile, built image, ran container on port **8080**, accessed and verified output (`Hello World from Docker multi-stage build`).
- [x] **Task 2:** Documented name, enrollment number, application execution output, and `docker ps` output confirming port 8080.
- [x] **Task 3:** Deployed 3 distinct applications (Node.js, Python, Java) using Docker and included verification outputs.
- [x] **Branch:** Checked out branch `session6-docker` ready for GitHub push.
