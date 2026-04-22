# Docker Compose — Complete Teaching Guide

## 1. What is Docker Compose?

Docker Compose is a tool used to define and run multi-container Docker applications using a single YAML file.

Instead of running multiple `docker run` commands, you describe everything in one file (`docker-compose.yml`) and start all services together.

---

## 2. Why Use Docker Compose?

* Manage multiple containers (frontend, backend, database)
* Run everything with one command:

  ```bash
  docker compose up
  ```
* Ensure consistent environments across systems
* Built-in networking between services
* Easier configuration and maintenance

---

## 3. Basic Structure of docker-compose.yml

```yaml
version: "3.9"

services:
  service_name:
    image: image_name
```

* `version`: Compose file format version
* `services`: Defines all containers

---

## 4. Important Service Options

### image

Specifies the Docker image:

```yaml
image: node:18
```

### build

Build image from Dockerfile:

```yaml
build: .
```

### container_name

Assign custom container name.

### ports

Maps host to container ports:

```yaml
ports:
  - "3000:3000"
```

### environment

Set environment variables:

```yaml
environment:
  - NODE_ENV=production
```

### env_file

Load variables from `.env` file:

```yaml
env_file:
  - .env
```

### depends_on

Controls startup order:

```yaml
depends_on:
  - db
```

### volumes

Attach storage:

```yaml
volumes:
  - data:/app/data
```

### networks

Connect services to a network.

---

## 5. Docker Networks

### What is a Network?

A network allows containers to communicate with each other.

### Why Needed?

* Isolation
* Internal communication using service names

---

### Create Network (CLI)

```bash
docker network create my-network
```

### Use Network (CLI)

```bash
docker run -d --network my-network nginx
```

---

### Networks in Docker Compose

```yaml
networks:
  my-network:
```

Attach to service:

```yaml
services:
  app:
    networks:
      - my-network
```

---

## 6. Docker Volumes

### What is a Volume?

Persistent storage outside the container.

### Why Needed?

Data remains even if container is deleted.

---

### Types of Volumes

* Named volumes (recommended)
* Bind mounts (local folder mapping)
* Anonymous volumes (temporary)

---

### Volume (CLI)

```bash
docker volume create my-volume
```

```bash
docker run -v my-volume:/data nginx
```

---

### Volumes in Docker Compose

```yaml
volumes:
  my-volume:
```

Attach to service:

```yaml
services:
  db:
    volumes:
      - my-volume:/data/db
```

---

## 7. Environment Variables & Build Args

### Environment Variables

```yaml
environment:
  - DB_HOST=db
  - PORT=5000
```

---

### Using .env File

```yaml
env_file:
  - .env
```

---

### Build Arguments

```yaml
build:
  context: .
  args:
    NODE_ENV: production
```

* Used during image build (not runtime)

---

## 8. Resource Limits (CPU & Memory)

### Why Needed?

* Prevent containers from using all system resources
* Maintain system stability

---

### Memory Limit

```yaml
mem_limit: 512m
```

* Example: `512m`, `1g`
* Exceeding limit may stop container

---

### CPU Limit

```yaml
cpus: "0.5"
```

* `0.5` = half CPU core
* `1.0` = one CPU core

---

### Swarm Mode Alternative (Advanced)

```yaml
deploy:
  resources:
    limits:
      cpus: "0.5"
      memory: 512M
```

Note: Works mainly in Docker Swarm mode.

---

## 9. Healthcheck

### What is Healthcheck?

Used to monitor if a container is working correctly.

Container states:

* healthy
* unhealthy

---

### Basic Syntax

```yaml
healthcheck:
  test: ["CMD", "curl", "-f", "http://localhost:5000"]
  interval: 30s
  timeout: 10s
  retries: 3
```

---

### Fields

* **test**: Command to check health
* **interval**: Time between checks
* **timeout**: Max time for check
* **retries**: Failures before unhealthy
* **start_period**: Grace time before checks start

Example:

```yaml
start_period: 20s
```

---

## 10. Full Example (Frontend + Backend + MongoDB)

```yaml
version: "3.9"

services:

  frontend:
    image: react-app-image
    container_name: frontend
    ports:
      - "3000:3000"
    depends_on:
      - backend
    networks:
      - app-network

  backend:
    image: node-backend-image
    container_name: backend
    ports:
      - "5000:5000"
    environment:
      - DB_URI=mongodb://db:27017/mydb
    depends_on:
      - db
    networks:
      - app-network

    mem_limit: 512m
    cpus: "0.5"

    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:5000/health"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 20s

  db:
    image: mongo
    container_name: mongodb
    volumes:
      - mongo-data:/data/db
    networks:
      - app-network

networks:
  app-network:

volumes:
  mongo-data:
```

---

## 11. Key Concepts to Remember

* Services communicate using service names (e.g., `db`)
* `depends_on` only controls startup order, not readiness
* Volumes ensure data persistence
* Networks enable internal communication
* Environment variables simplify configuration
* Resource limits prevent system overload
* Healthchecks improve reliability

---

## 12. Final Commands

Start all services:

```bash
docker compose up
```

Stop services:

```bash
docker compose down
```

---

## 13. Summary

Docker Compose simplifies multi-container applications by:

* Centralizing configuration
* Automating networking
* Managing dependencies
* Ensuring consistency
* Improving development workflow

---

This is a complete foundation for understanding and using Docker Compose effectively.
