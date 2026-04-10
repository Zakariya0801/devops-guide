# 🐳 Docker Learning Guide (Beginner → Intermediate)

This guide covers:
- Why Docker was introduced
- Core concepts (Images, Containers)
- Application basics (Node.js & FastAPI)
- Dockerfile (all important directives)
- Docker commands & flags
- Multi-stage builds
- Security practices (USER, ARG vs ENV)


You can view the following video on docker to have a more easier and detailed understanding.
[Watch Video](https://drive.google.com/file/d/15iFwU037-VRuXRkEEIgj7lkIXDCy89ys/view?usp=drive_link)

---

# 📌 1. Why Docker Was Introduced

### Problems Before Docker
- "It works on my machine" issue
- Dependency/version conflicts
- Difficult environment setup
- OS incompatibility (Windows vs Linux)
- Hard to scale and deploy

### What Docker Solves
- Consistent environments everywhere
- Lightweight isolation (containers)
- Easy deployment and scaling
- Portable applications

---

# ⚙️ 2. What Docker Does

- Packages app + dependencies → **Image**
- Runs it → **Container**
- Uses OS-level virtualization
- Faster and lighter than virtual machines

---

# 📦 3. Docker Images vs Containers

### Image
- Blueprint/template
- Read-only
- Contains:
  - Code
  - Dependencies
  - Runtime

### Container
- Running instance of image
- Can be started/stopped/deleted
- Isolated environment

---

# 🧠 4. Understanding Applications (Before Docker)

## Node.js App
- `package.json` → config
- `npm install` → installs dependencies
- `node app.js` → runs app
- Optional:
  - `npm run build`
  - `npm start`

### Key Concepts
- Dependencies stored in `node_modules`
- Requires Node runtime
- Uses environment variables

---

## FastAPI App (Python)
- `requirements.txt` → dependencies
- `pip install -r requirements.txt`
- Run:
  ```bash
  uvicorn main:app --reload
  ```

### Key Concepts
- Python version matters
- Virtual environments
- Uvicorn = server

---

# 📄 5. Dockerfile

## Core Directives

| Directive    | Description                            |
|--------------|----------------------------------------|
| `FROM`       | Base image                             |
| `WORKDIR`    | Set working directory                  |
| `COPY`       | Copy files from host                   |
| `ADD`        | Similar to COPY (less preferred)       |
| `RUN`        | Execute commands during build          |
| `CMD`        | Default command when container starts  |
| `ENTRYPOINT` | Fixed command execution                |
| `EXPOSE`     | Document port                          |
| `ENV`        | Runtime environment variables          |

## Advanced Directives

| Directive | Description               |
|-----------|---------------------------|
| `ARG`     | Build-time variables      |
| `VOLUME`  | Persistent storage        |
| `USER`    | Run container as non-root |
| `LABEL`   | Metadata                  |
| `SHELL`   | Change shell              |

## 🔑 Important Dockerfile Concepts
- Each instruction creates a layer
- Layer caching improves performance
- Order of instructions matters
- Keep images small
- Use multi-stage builds

---

## 🔄 ARG vs ENV

### ARG
- Build-time only
- Not available in running container
- Used with:
  ```bash
  docker build --build-arg
  ```

### ENV
- Available at runtime
- Stored in image
- Accessible inside container

### Combined Usage
```dockerfile
ARG APP_ENV=production
ENV APP_ENV=$APP_ENV
```

---

## 🔐 USER (Security)

### Why use USER
- Containers run as root by default (unsafe)
- Limits damage if app is compromised

### Best Practice
- Build as root
- Run as non-root

### Example
```dockerfile
RUN useradd -m appuser
USER appuser
```

---

## 🧪 Example Dockerfile Flows

### Node.js
1. Copy `package.json`
2. Install dependencies
3. Copy code
4. Run app

### FastAPI
1. Copy `requirements.txt`
2. Install dependencies
3. Copy app
4. Run uvicorn

---

Before moving on to Docker commands, watch the video for a practical overview.
[Watch Video](https://drive.google.com/file/d/12yh4WfUCMCk3v65g9665LXKOg2o8BWvW/view?usp=drive_link)

# 🐳 6. Docker Commands

## Image Commands

### Build Image
```bash
docker build -t myapp .
```

### Important Flags
| Flag | Description       |
|------|-------------------|
| `-t` | Tag               |
| `-f` | Custom Dockerfile |

### Other Commands
```bash
docker images
docker rmi <image>
```

---

## Container Commands

### Run Container
```bash
docker run -d -p 3000:3000 --name app myapp
```

### Important Flags
| Flag     | Description        |
|----------|--------------------|
| `-d`     | Detached mode      |
| `-p`     | Port mapping       |
| `--name` | Container name     |
| `-e`     | Environment variables |
| `-v`     | Volume mount       |

### Other Commands
```bash
docker ps
docker ps -a
docker stop <id>
docker start <id>
docker restart <id>
docker rm <id>
```

---

## Debugging
```bash
docker logs <id>
docker exec -it <id> bash
docker inspect <id>
docker stats
```

## Cleanup
```bash
docker system prune
docker image prune
docker container prune
```

---

# 🌐 7. Networking & Volumes

## Networking
- Default: bridge network
- Port mapping using `-p`
- Containers can communicate internally

## Volumes
- Persist data outside container
- Types:
  - Named volumes
  - Bind mounts

---

# 🧱 8. Multi-Stage Builds

### What they do
- Use multiple `FROM` statements
- Separate build and runtime environments
- Copy only required files

### Why use them
- Reduce image size
- Improve security
- Remove unnecessary tools

### Example
```dockerfile
# Build stage
FROM node:18 AS builder
WORKDIR /app
COPY . .
RUN npm install && npm run build

# Production stage
FROM nginx:alpine
COPY --from=builder /app/dist /usr/share/nginx/html
```

### Key Concepts
- `AS builder` → name the stage
- `COPY --from=builder` → copy from previous stage
- Final stage = final image

---

# 🚀 Best Practices

- Use small base images (`alpine`/`slim`)
- Minimize layers
- Use `.dockerignore`
- Avoid root user
- Cache dependencies properly
- Keep images clean