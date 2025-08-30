                                        # Complete Node.js Docker Project Setup Guide

This guide will walk you through creating a Node.js project with Docker from scratch, including all necessary commands and Docker concepts.

## 📋 Prerequisites

- Docker installed on your system
- Basic understanding of Node.js
- Text editor or IDE

## 🚀 Quick Start

### Step 1: Create Project Directory

```bash
# Create and navigate to your project folder
mkdir my-nodejs-app
cd my-nodejs-app
```

### Step 2: Create Your Application File

Create `start.js` file:

```javascript
const express = require("express");
const app = express();
const port = 3000;

app.get("/", (req, res) => {
  res.send("Hello from Docker-only Node.js app!");
});

app.listen(port, () => {
  console.log(`Server running on http://localhost:${port}`);
});
```

### Step 3: Create Dockerfile

Create `Dockerfile` (no extension):

```dockerfile
FROM node:18-alpine

# Set working directory
WORKDIR /app

# Copy application files
COPY start.js .

# Install dependencies directly (without package.json)
RUN npm install express

# Optional: Install nodemon for development
# RUN npm install -g nodemon

# Expose port
EXPOSE 3000

# Main command to run when container starts
CMD ["node", "start.js"]
```

### Step 4: Build and Run

```bash
# Build the Docker image
docker build -t node-no-package .

# Run the container
docker run -it --rm -p 3000:3000 node-no-package
```

Visit `http://localhost:3000` to see your app running!

## 📚 Dockerfile Commands Explained

### Core Docker Instructions

| Command   | Description                                    | When It Executes   | Example                    |
| --------- | ---------------------------------------------- | ------------------ | -------------------------- |
| `FROM`    | Specifies base image                           | Image build time   | `FROM node:18-alpine`      |
| `WORKDIR` | Sets working directory for subsequent commands | Image build time   | `WORKDIR /app`             |
| `COPY`    | Copies files from host to container            | Image build time   | `COPY start.js .`          |
| `RUN`     | Executes commands during image build           | Image build time   | `RUN npm install express`  |
| `CMD`     | Default command when container starts          | Container runtime  | `CMD ["node", "start.js"]` |
| `EXPOSE`  | Documents which port the app uses              | Documentation only | `EXPOSE 3000`              |

### 🔍 RUN vs CMD - Key Differences

#### RUN Command

- **When**: Executes during **image building**
- **Purpose**: Install packages, setup environment, create directories
- **Layer**: Creates a new layer in the image
- **Examples**:
  ```dockerfile
  RUN npm install express
  RUN npm install -g nodemon
  RUN apt-get update && apt-get install -y curl
  ```

#### CMD Command

- **When**: Executes when **container starts**
- **Purpose**: Define the main process/application to run
- **Limit**: Only one CMD per Dockerfile (last one wins)
- **Examples**:
  ```dockerfile
  CMD ["node", "start.js"]
  CMD ["npm", "start"]
  CMD ["nodemon", "start.js"]
  ```

## 🐳 Docker Commands Reference

### Building Images

```bash
# Build image with tag
docker build -t <image-name> .

# Build with specific tag version
docker build -t my-app:1.0.0 .

# Build from specific directory
docker build -t my-app /path/to/dockerfile/directory
```

### Running Containers

```bash
# Basic run
docker run <image-name>

# Run with port mapping
docker run -p <host-port>:<container-port> <image-name>

# Run interactively with auto-removal
docker run -it --rm <image-name>

# Run in background (detached)
docker run -d <image-name>

# Run with custom name
docker run --name my-container <image-name>



#to run image and its nodmon

docker run -it --rm -p 3000:3000 \
  -v $(pwd):/app \
  -v /app/node_modules \
  myapp
```

### Docker Run Flags Explained

| Flag     | Description                         | Example                                           |
| -------- | ----------------------------------- | ------------------------------------------------- |
| `-it`    | Interactive terminal                | `docker run -it my-app`                           |
| `--rm`   | Auto-remove container when it stops | `docker run --rm my-app`                          |
| `-p`     | Port mapping (host:container)       | `docker run -p 3000:3000 my-app`                  |
| `-d`     | Run in background (detached)        | `docker run -d my-app`                            |
| `--name` | Assign name to container            | `docker run --name my-container my-app`           |
| `-v`     | Volume mounting                     | `docker run -v /host/path:/container/path my-app` |

## 📁 Project Structure Options

### Option 1: Minimal Setup (No package.json)

```
my-nodejs-app/
├── Dockerfile
└── start.js
```

### Option 2: With Package.json

```
my-nodejs-app/
├── Dockerfile
├── package.json
├── package-lock.json
└── src/
    └── index.js
```

For Option 2, your Dockerfile would be:

```dockerfile
FROM node:18-alpine
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 3000
CMD ["npm", "start"]
```

## 🛠️ Development vs Production Dockerfiles

### Development Dockerfile

```dockerfile
FROM node:18-alpine
WORKDIR /app
COPY package*.json ./
RUN npm install
# Install nodemon for development
RUN npm install -g nodemon
COPY . .
EXPOSE 3000
# Use nodemon for auto-restart
CMD ["nodemon", "index.js"]
```

### Production Dockerfile

```dockerfile
FROM node:18-alpine
WORKDIR /app
COPY package*.json ./
# Install only production dependencies
RUN npm ci --only=production
COPY . .
EXPOSE 3000
# Create non-root user for security
RUN addgroup -g 1001 -S nodejs
RUN adduser -S nextjs -u 1001
USER nextjs
CMD ["node", "index.js"]
```

## 🔧 Common Docker Management Commands

### Image Management

```bash
# List all images
docker images

# Remove image
docker rmi <image-name>

# Remove all unused images
docker image prune
```

### Container Management

```bash
# List running containers
docker ps

# List all containers
docker ps -a

# Stop container
docker stop <container-id>

# Remove container
docker rm <container-id>

# View container logs
docker logs <container-id>
```

### System Cleanup

```bash
# Remove all stopped containers
docker container prune

# Remove all unused images
docker image prune

# Remove everything unused
docker system prune
```

## 🚨 Troubleshooting

### Common Issues and Solutions

1. **Port already in use**

   ```bash
   # Use different host port
   docker run -p 3001:3000 my-app
   ```

2. **Permission denied**

   ```bash
   # Run with sudo (Linux/Mac)
   sudo docker build -t my-app .
   ```

3. **Image not found**

   ```bash
   # Check if image was built successfully
   docker images
   ```

4. **Container stops immediately**
   ```bash
   # Check logs
   docker logs <container-id>
   ```

## 📝 Best Practices

1. **Use specific Node.js versions**: `FROM node:18-alpine` instead of `FROM node`
2. **Use .dockerignore**: Exclude unnecessary files
3. **Multi-stage builds**: For smaller production images
4. **Don't run as root**: Create and use non-root users
5. **Use COPY instead of ADD**: Unless you need ADD's extra features
6. **Minimize layers**: Combine RUN commands when possible

## 🎯 Next Steps

- Learn about Docker Compose for multi-container applications
- Explore container orchestration with Kubernetes
- Implement CI/CD pipelines with Docker
- Study Docker security best practices

## 📚 Additional Resources

- [Docker Official Documentation](https://docs.docker.com/)
- [Node.js Docker Best Practices](https://nodejs.org/en/docs/guides/nodejs-docker-webapp/)
- [Dockerfile Reference](https://docs.docker.com/engine/reference/builder/)

---

**Happy Dockerizing! 🐳**
