# Docker Homework Submission

---

## 👤 Student Details

* **Name:** Nachiket R
* **Enrollment Number:** 24bcs10131
* **Repository:** [devops-heros](https://github.com/Nachiket115/devops-heros)
* **Branch:** `session6-docker`

---

## 📂 Project Structure

```
session6-7-docker/
├── Apache-app/
│   ├── Dockerfile
│   └── index.html
├── React-app/
│   ├── Dockerfile
│   ├── index.html
│   ├── package.json
│   ├── vite.config.js
│   └── src/
│       ├── App.jsx
│       └── main.jsx
├── java-app/
│   ├── App.java
│   └── Dockerfile
├── multi-stage-dockerfile/
│   ├── Dockerfile
│   ├── package.json
│   └── server.js
├── nginx-app/
│   ├── Dockerfile
│   └── index.html
├── nodejs-app/
│   ├── Dockerfile
│   ├── package.json
│   └── server.js
├── python-app/
│   ├── Dockerfile
│   ├── app.py
│   └── requirements.txt
├── DOCKER_HOMEWORK_SUBMISSION.md
└── docker.md
```

---

## 🎯 Part 1: Hello World Applications

### 1. Node.js Application (`nodejs-app/`)

#### Files:
* **`package.json`**:
```json
{
  "name": "nodejs-hello-world",
  "version": "1.0.0",
  "main": "server.js",
  "scripts": {
    "start": "node server.js"
  },
  "dependencies": {
    "express": "^4.19.2"
  }
}
```

* **`server.js`**:
```javascript
const express = require("express");
const app = express();
const PORT = 3000;

app.get("/", (req, res) => {
  res.send("<h1>Hello World from Node.js!</h1>");
});

app.listen(PORT, () => {
  console.log(`Node.js server is running on port ${PORT}`);
});
```

* **`Dockerfile`**:
```dockerfile
FROM node:20-alpine
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 3000
CMD ["npm", "start"]
```

#### Build & Run Commands:
```bash
cd session6-7-docker/nodejs-app
docker build -t nodejs-hello .
docker run -d --name nodejs-container -p 3000:3000 nodejs-hello
```

#### Verification Output:
```bash
$ curl http://localhost:3000
<h1>Hello World from Node.js!</h1>
```

---

### 2. Python Application (`python-app/`)

#### Files:
* **`requirements.txt`**:
```
flask==3.0.3
```

* **`app.py`**:
```python
from flask import Flask

app = Flask(__name__)

@app.route("/")
def hello():
    return "<h1>Hello World from Python!</h1>"

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000)
```

* **`Dockerfile`**:
```dockerfile
FROM python:3.11-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY app.py .
EXPOSE 5000
CMD ["python", "app.py"]
```

#### Build & Run Commands:
```bash
cd session6-7-docker/python-app
docker build -t python-hello .
docker run -d --name python-container -p 5001:5000 python-hello
```

#### Verification Output:
```bash
$ curl http://localhost:5001
<h1>Hello World from Python!</h1>
```

---

### 3. Java Application (`java-app/`)

#### Files:
* **`App.java`**:
```java
import com.sun.net.httpserver.HttpServer;
import com.sun.net.httpserver.HttpHandler;
import com.sun.net.httpserver.HttpExchange;
import java.io.IOException;
import java.io.OutputStream;
import java.net.InetSocketAddress;

public class App {
    public static void main(String[] args) throws IOException {
        int port = 8080;
        HttpServer server = HttpServer.create(new InetSocketAddress(port), 0);
        server.createContext("/", new HttpHandler() {
            @Override
            public void handle(HttpExchange exchange) throws IOException {
                String response = "<h1>Hello World from Java!</h1>";
                exchange.getResponseHeaders().set("Content-Type", "text/html; charset=UTF-8");
                exchange.sendResponseHeaders(200, response.getBytes().length);
                OutputStream os = exchange.getResponseBody();
                os.write(response.getBytes());
                os.close();
            }
        });
        System.out.println("Java HTTP Server is listening on port " + port);
        server.start();
    }
}
```

* **`Dockerfile`**:
```dockerfile
FROM eclipse-temurin:17-jdk
WORKDIR /app
COPY App.java .
RUN javac App.java
EXPOSE 8080
CMD ["java", "App"]
```

#### Build & Run Commands:
```bash
cd session6-7-docker/java-app
docker build -t java-hello .
docker run -d --name java-container -p 8082:8080 java-hello
```

#### Verification Output:
```bash
$ curl http://localhost:8082
<h1>Hello World from Java!</h1>
```

---

### 4. Apache Web Server (`Apache-app/`)

#### Files:
* **`index.html`**:
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Apache Docker App</title>
</head>
<body>
    <h1>Hello World from Apache Web Server!</h1>
</body>
</html>
```

* **`Dockerfile`**:
```dockerfile
FROM httpd:alpine
COPY index.html /usr/local/apache2/htdocs/index.html
EXPOSE 80
```

#### Build & Run Commands:
```bash
cd session6-7-docker/Apache-app
docker build -t apache-hello .
docker run -d --name apache-container -p 8083:80 apache-hello
```

#### Verification Output:
```bash
$ curl http://localhost:8083
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Apache Docker App</title>
</head>
<body>
    <h1>Hello World from Apache Web Server!</h1>
</body>
</html>
```

---

### 5. React Application (`React-app/`)

#### Files:
* **`Dockerfile` (Multi-stage build with Vite & Nginx)**:
```dockerfile
# Stage 1: Build the React application
FROM node:20-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

# Stage 2: Serve the built static files with Nginx
FROM nginx:alpine
COPY --from=builder /app/dist /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

* **`src/App.jsx`**:
```jsx
import React from 'react';

function App() {
  return (
    <div style={{ textAlign: 'center', marginTop: '50px', fontFamily: 'sans-serif' }}>
      <h1>Hello World from React!</h1>
    </div>
  );
}

export default App;
```

#### Build & Run Commands:
```bash
cd session6-7-docker/React-app
docker build -t react-hello .
docker run -d --name react-container -p 8084:80 react-hello
```

#### Verification Output:
```bash
$ curl http://localhost:8084
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>React Docker App</title>
    <script type="module" crossorigin src="/assets/index-Djmr0IdF.js"></script>
  </head>
  <body>
    <div id="root"></div>
  </body>
</html>
```

---

### 6. Nginx Application (`nginx-app/`)

#### Files:
* **`index.html`**:
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Nginx Docker App</title>
</head>
<body>
    <h1>Hello World from Nginx!</h1>
</body>
</html>
```

* **`Dockerfile`**:
```dockerfile
FROM nginx:alpine
COPY index.html /usr/share/nginx/html/index.html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

#### Build & Run Commands:
```bash
cd session6-7-docker/nginx-app
docker build -t nginx-hello .
docker run -d --name nginx-container -p 8085:80 nginx-hello
```

#### Verification Output:
```bash
$ curl http://localhost:8085
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Nginx Docker App</title>
</head>
<body>
    <h1>Hello World from Nginx!</h1>
</body>
</html>
```

---

## 🚀 Part 2: Docker Multi-Stage Build Homework

### Task 1: Build and Run Multi-Stage Dockerfile on Port 8080

#### Dockerfile ([`multi-stage-dockerfile/Dockerfile`](file:///Users/nachiketr/devops-heros/session6-7-docker/multi-stage-dockerfile/Dockerfile)):
```dockerfile
# -------------------------
# Stage 1: Build
# -------------------------
FROM node:24-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .

# -------------------------
# Stage 2: Production
# -------------------------
FROM node:24-alpine AS production
WORKDIR /app
COPY --from=builder /app/package*.json ./
RUN npm install --omit=dev
COPY --from=builder /app/server.js ./
EXPOSE 3000
CMD ["npm", "start"]
```

#### Build Command:
```bash
cd session6-7-docker/multi-stage-dockerfile
docker build -t multistage-hello .
```

#### Run Container Command (Running on Host Port 8080):
```bash
docker run -d --name multistage-container -p 8080:3000 multistage-hello
```

#### Access & Verification (Port 8080):
```bash
$ curl -i http://localhost:8080

HTTP/1.1 200 OK
X-Powered-By: Express
Content-Type: text/html; charset=utf-8
Content-Length: 51
Date: Wed, 02 Sep 2026 10:17:58 GMT
Connection: keep-alive

<h1>Hello World from Docker Multi-Stage Build!</h1>
```

---

### Task 2: Documentation & Container Status (`docker ps`)

```bash
$ docker ps
CONTAINER ID   IMAGE              COMMAND                  CREATED          STATUS          PORTS                                         NAMES
f532c8081e2a   multistage-hello   "docker-entrypoint.s…"   2 minutes ago    Up 2 minutes    0.0.0.0:8080->3000/tcp, [::]:8080->3000/tcp   multistage-container
a4b969e11504   python-hello       "python app.py"          1 minute ago     Up 1 minute     0.0.0.0:5001->5000/tcp, [::]:5001->5000/tcp   python-container
e296700c620d   nginx-hello        "/docker-entrypoint.…"   2 minutes ago    Up 2 minutes    0.0.0.0:8085->80/tcp, [::]:8085->80/tcp       nginx-container
faca2b28c1f4   react-hello        "/docker-entrypoint.…"   2 minutes ago    Up 2 minutes    0.0.0.0:8084->80/tcp, [::]:8084->80/tcp       react-container
08dffa8deece   apache-hello       "httpd-foreground"       2 minutes ago    Up 2 minutes    0.0.0.0:8083->80/tcp, [::]:8083->80/tcp       apache-container
3351051c13a1   java-hello         "/__cacert_entrypoin…"   2 minutes ago    Up 2 minutes    0.0.0.0:8082->8080/tcp, [::]:8082->8080/tcp   java-container
e022f62a4d89   nodejs-hello       "docker-entrypoint.s…"   2 minutes ago    Up 2 minutes    0.0.0.0:3000->3000/tcp, [::]:3000->3000/tcp   nodejs-container
```

---

### Task 3: Docker Application Deployment (Node.js, Python, Java)

| Application Type | Technology Stack | Container Port | Host Port | Verified URL / Output |
| :--- | :--- | :--- | :--- | :--- |
| **Node.js** | Node.js 20 Alpine + Express | `3000` | `3000` | `http://localhost:3000` &rarr; `<h1>Hello World from Node.js!</h1>` |
| **Python** | Python 3.11 Slim + Flask | `5000` | `5001` | `http://localhost:5001` &rarr; `<h1>Hello World from Python!</h1>` |
| **Java** | Eclipse Temurin OpenJDK 17 | `8080` | `8082` | `http://localhost:8082` &rarr; `<h1>Hello World from Java!</h1>` |
| **Multi-Stage Node** | Multi-Stage Node 24 Alpine | `3000` | `8080` | `http://localhost:8080` &rarr; `<h1>Hello World from Docker Multi-Stage Build!</h1>` |

---

## 🏁 Summary Checklist

- [x] Created `nodejs-app` with Dockerfile and verified output
- [x] Created `python-app` with Dockerfile and verified output
- [x] Created `java-app` with Dockerfile and verified output
- [x] Created `Apache-app` with Dockerfile and verified output
- [x] Created `React-app` with Dockerfile and verified output
- [x] Created `nginx-app` with Dockerfile and verified output
- [x] Built and ran `multi-stage-dockerfile` on port `8080`
- [x] Verified running containers using `docker ps`
- [x] Prepared documentation Markdown file
- [x] Created branch `session6-docker` for GitHub submission
