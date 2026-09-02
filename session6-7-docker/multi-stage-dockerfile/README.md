# Docker Multi-Stage Build Application

## 👤 Student Details
* **Name:** Nachiketas Iyer
* **Enrollment Number:** 24bcs10131

---

## 📖 Overview
This project demonstrates **Docker Multi-Stage Builds** in Node.js. 

### Why Multi-Stage Builds?
* **Stage 1 (Builder):** Uses full build dependencies (`npm install`) to prepare the code.
* **Stage 2 (Production):** Creates a minimal, lean production container by copying only the necessary runtime files and omitting dev dependencies (`npm install --omit=dev`), significantly reducing final image size and attack surface.

---

## 📁 Files in this Directory
* [**`Dockerfile`**](file:///Users/nachiketr/devops-heros/session6-7-docker/multi-stage-dockerfile/Dockerfile) - Two-stage build definition (`builder` & `production`).
* [**`package.json`**](file:///Users/nachiketr/devops-heros/session6-7-docker/multi-stage-dockerfile/package.json) - Node.js dependencies (Express).
* [**`server.js`**](file:///Users/nachiketr/devops-heros/session6-7-docker/multi-stage-dockerfile/server.js) - Express web server listening on port 3000.

---

## 🛠️ Task 1: Build & Run Commands

### 1. Build the Docker Image
```bash
docker build -t multistage-app .
```

### 2. Run Container on Port 8080
Run a container mapping host port **`8080`** to container port **`3000`**:
```bash
docker run -d --name multistage-app -p 8080:3000 multistage-app
```

### 3. Access & Verify Output
```bash
curl -i http://localhost:8080
```

#### Verification Response:
```http
HTTP/1.1 200 OK
X-Powered-By: Express
Content-Type: text/html; charset=utf-8
Content-Length: 50
Connection: keep-alive

<h1>Hello World from Docker multi-stage build</h1>
```

---

## 📊 Task 2: Verification with `docker ps`

```bash
$ docker ps --filter "name=multistage-app"
CONTAINER ID   IMAGE            COMMAND                  CREATED         STATUS         PORTS                                         NAMES
559ec91e3574   multistage-app   "docker-entrypoint.s…"   9 seconds ago   Up 8 seconds   0.0.0.0:8080->3000/tcp, [::]:8080->3000/tcp   multistage-app
```

---

## 🚀 Task 3: Deployments Evidence (Node.js, Python, Java)

| Application | Container Port | Host Port | Status | Verification Response |
| :--- | :--- | :--- | :--- | :--- |
| **Multi-Stage Node.js** | `3000` | `8080` | `Up` | `<h1>Hello World from Docker multi-stage build</h1>` |
| **Node.js Express** | `3000` | `3000` | `Up` | `<h1>Hello World from Node.js!</h1>` |
| **Python Flask** | `5000` | `5001` | `Up` | `<h1>Hello World from Python!</h1>` |
| **Java HTTP Server** | `8080` | `8082` | `Up` | `<h1>Hello World from Java!</h1>` |
