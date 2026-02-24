# MEAN Stack DevOps — CRUD Application

A full-stack **MEAN** (MongoDB, Express, Angular 15, Node.js) CRUD application containerized with Docker, deployed on an Azure VM, and automated with a GitHub Actions CI/CD pipeline.

## Architecture

```
Internet → Port 80 → [Nginx Reverse Proxy]
                          ├── /         → [Angular Frontend (Nginx)]
                          └── /api/     → [Node.js + Express Backend]
                                              └── [MongoDB 6.0]
```

## Tech Stack

| Layer       | Technology              |
|-------------|-------------------------|
| Frontend    | Angular 15              |
| Backend     | Node.js + Express       |
| Database    | MongoDB 6.0             |
| Proxy       | Nginx (reverse proxy)   |
| Container   | Docker + Docker Compose |
| CI/CD       | GitHub Actions          |
| Cloud       | Azure VM (Ubuntu 22.04) |

## Docker Hub Images

- [`bharathreddy25/mean-backend`](https://hub.docker.com/r/bharathreddy25/mean-backend)
- [`bharathreddy25/mean-frontend`](https://hub.docker.com/r/bharathreddy25/mean-frontend)
- `mongo:6.0` (official image)

## Project Structure

```
├── frontend/
│   ├── Dockerfile              # Multi-stage: Angular build → Nginx serve
│   ├── nginx.conf              # SPA routing (try_files)
│   ├── .dockerignore
│   └── (Angular source files)
├── backend/
│   ├── Dockerfile              # Node.js production image
│   ├── .dockerignore
│   └── (Express source files)
├── nginx/
│   ├── Dockerfile              # Reverse proxy image
│   └── nginx.conf              # Routes / → frontend, /api/ → backend
├── .github/workflows/
│   └── deploy.yml              # CI/CD pipeline
├── docker-compose.yml          # 4-service orchestration
├── .gitignore
└── README.md
```

## Prerequisites

- [Docker](https://docs.docker.com/get-docker/) & Docker Compose
- [Git](https://git-scm.com/)
- [Node.js 18+](https://nodejs.org/) (for local development only)
- Azure/AWS account (for cloud deployment)

## Local Setup

### 1. Clone the Repository
```bash
git clone https://github.com/kbpr21/crud-dd-task-mean-app.git
cd crud-dd-task-mean-app
```

### 2. Build Docker Images
```bash
docker build -t bharathreddy25/mean-backend:latest ./backend
docker build -t bharathreddy25/mean-frontend:latest ./frontend
```

### 3. Start the Application
```bash
docker-compose up -d
```

### 4. Verify
- Open `http://localhost` → Angular UI loads
- API endpoint: `http://localhost/api/tutorials`
- Check logs: `docker-compose logs backend`

### 5. Stop
```bash
docker-compose down
```

## Environment Variables

| Variable    | Service  | Default                            | Description          |
|-------------|----------|------------------------------------|----------------------|
| `MONGO_URI` | backend  | `mongodb://mongodb:27017/dd_db`    | MongoDB connection   |
| `PORT`      | backend  | `8080`                             | Backend server port  |

## Azure VM Deployment

### 1. Create Ubuntu VM
- Image: Ubuntu Server 22.04 LTS
- Size: Standard_B1s (or similar)
- Open ports: SSH (22), HTTP (80)

### 2. Install Docker on VM
```bash
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
sudo usermod -aG docker $USER
newgrp docker
sudo apt-get install -y docker-compose-plugin
```

### 3. Deploy
```bash
mkdir ~/mean-app && cd ~/mean-app
mkdir nginx
# Copy docker-compose.yml, nginx/nginx.conf, nginx/Dockerfile to the VM
docker pull bharathreddy25/mean-backend:latest
docker pull bharathreddy25/mean-frontend:latest
docker compose up -d
```

### 4. Verify
Open `http://<VM-PUBLIC-IP>` in browser.

## CI/CD Pipeline

The GitHub Actions pipeline (`.github/workflows/deploy.yml`) triggers on every push to `main`:

1. **Build & Push** — Builds Docker images for frontend and backend, pushes to Docker Hub.
2. **Deploy** — SSHs into the Azure VM, pulls latest images, restarts containers.

### Required GitHub Secrets

| Secret Name         | Value                                    |
|---------------------|------------------------------------------|
| `DOCKERHUB_USERNAME`| Docker Hub username (`bharathreddy25`)   |
| `DOCKERHUB_TOKEN`   | Docker Hub access token                  |
| `AZURE_VM_HOST`     | VM public IP address                     |
| `AZURE_VM_USER`     | VM SSH username (`azureuser`)            |
| `AZURE_VM_SSH_KEY`  | Full contents of `.pem` private key file |

### Setup Secrets
Go to GitHub repo → **Settings** → **Secrets and variables** → **Actions** → **New repository secret**

## Screenshots

<!-- Add your screenshots below -->

### GitHub Repository
<!-- ![GitHub Repo](screenshots/github-repo.png) -->

### Docker Hub Images
<!-- ![Docker Hub](screenshots/docker-hub.png) -->

### Application UI
<!-- ![App UI](screenshots/app-ui.png) -->

### Docker Compose Running
<!-- ![Docker PS](screenshots/docker-ps.png) -->

### Azure VM
<!-- ![Azure VM](screenshots/azure-vm.png) -->

### CI/CD Pipeline
<!-- ![CI/CD](screenshots/cicd-pipeline.png) -->

## Application Features

- **Create** tutorials with title and description
- **List** all tutorials
- **Search** tutorials by title
- **Update** tutorial details and published status
- **Delete** individual or all tutorials

## Important Note

> The Azure VM infrastructure should be **stopped but not deleted** after completing the task, as it may be needed for a live demo in the next round.
