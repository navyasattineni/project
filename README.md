# MEAN CRUD Application — Docker & CI/CD

## 📖 Project Overview

This project is a full-stack **MEAN application** (MongoDB, Express, Angular, Node.js) containerized with Docker, deployed on an **Ubuntu VM (AWS EC2)**, and automated with **GitHub Actions CI/CD**. The entire app is served via **Nginx reverse proxy on port 80**.

---

## 1. Repository Setup

Create a new GitHub repository and push the project code:

```bash
git init
git remote add origin https://github.com/navyasattineni/project.git
git add .
git commit -m "Initial commit"
git push origin main
```

## 2. Containerization & Deployment  

### Frontend & Backend Dockerfiles  

- **Backend Dockerfile (Node.js/Express API)**  
  - Uses `node:18-alpine` base image  
  - Installs dependencies from `package.json`  
  - Copies code into the container  
  - Exposes port `8080` (API)  
  - Starts app with `node server.js`  

- **Frontend Dockerfile (Angular App with Nginx)**  
  - Uses `node:18-alpine` to build Angular app (`npm run build`)  
  - Copies the built Angular `dist/` files into `nginx:alpine`  
  - Replaces default Nginx config with custom `nginx.conf`  
  - Exposes port `80` (serves Angular + proxies `/api` to backend)  

### Build Docker images locally  
```bash
docker-compose build
```

### Push Docker images to Docker Hub
```bash

docker login
docker-compose push
```
### Set up Ubuntu VM (EC2)

1. Launch an Ubuntu EC2 instance on AWS.

2. Connect to the instance:
using EC2 Instance Connect from the AWS Console (browser terminal),

Or via SSH from your local machine:

### Install Docker & Compose on VM
```bash
sudo apt update
sudo apt install -y docker.io docker-compose
```
### Clone the repo on server:
```bash
git clone https://github.com/navyasattineni/project.git
```
### Deploy with Docker Compose:
```bash
docker-compose pull
docker-compose up -d
```

## 3. Database Setup
- Used official MongoDB Docker image via Docker Compose
- Data persisted with Docker volume
Command:
```bash
docker run -d --name mongo -p 27017:27017 mongo:6
```
## 4. Docker Compose Setup
The `docker-compose.yml` file (at the root of this repo) defines the stack:

- **mongo** → official `mongo:6` image, data stored in a Docker volume  
- **backend** → Node.js/Express API (`navyasattineni/mean-backend:latest`)  
- **frontend** → Angular app served by Nginx (`navyasattineni/mean-frontend:latest`)  

#### Commands
- Build & run:
```bash
  docker-compose build
  docker-compose up -d
  docker-compose ps
```
Stop services:
```bash
docker-compose down
```
Pull new images (CI/CD step):
```bash
docker-compose pull
docker-compose up -d
```
Check running containers:
```bash
docker-compose ps
```

## 5. CI/CD Pipeline (GitHub Actions)

- Created workflow .github/workflows/ci-cd.yml.
- Configured GitHub Secrets:
	- DOCKERHUB_USERNAME
	- DOCKERHUB_TOKEN

Workflow tasks:
- Checkout repo
- Login to Docker Hub
- Build & push images
- Deploy on EC2 self-hosted runner

Manual run on EC2 (if needed):
```bash
docker-compose pull
docker-compose up -d --force-recreate
```

## 6. Nginx Reverse Proxy (Inside Frontend Container)

Nginx serves Angular build on `/`.

Proxies API calls `/api/` → `backend:8080`.

Config file: `frontend/nginx.conf`.

### Useful checks
- See containers and port mapping:
  ```bash
  docker-compose ps
  ```
- Reload nginx after editing config (container name: frontend):
```bash
  docker exec -it frontend nginx -s reload
```
## 7. Project Structure

```bash
mean-crud-docker-cicd/
└── crud-dd-task-mean-app/
    ├── .github/
    │   └── workflows/
    │       └── ci-cd.yml       
    │
    ├── backend/
    │   └── Dockerfile          
    │
    ├── frontend/
    │   ├── Dockerfile           
    │   └── nginx.conf           
    │
    ├── docker-compose.yml      
    ├── README.md                
    └── screenshots/             
		├── compose-file.png              
		├── Dockerfiles.png              
		├── nginx-conf.png                
		├── dockerhub-images.png        
		├── docker-ps.png                            
		├── github-secrets.png                   
		├── ci-cd-yml.png                      
		├── frontend-build.png                     
		├── backend-build.png                    
		├── mongod-status-version.png        
		├── app-browser.png
		└── app-browser-2.png      
```

## 📸 Screenshots

Here are the required screenshots for verification:

### 1. Docker & Compose Setup
- ![docker-compose](screenshots/compose-file.png)
- ![dockerfiles](screenshots/Dockerfiles.png)
- ![nginx-conf](screenshots/nginx-conf.png)
- ![dockerhub-images](screenshots/dockerhub-images.png)
- ![docker-ps](screenshots/docker-ps.png)

### 2. CI/CD Pipeline
- ![github-secrets](screenshots/github-secrets.png)
- ![ci-cd-yml](screenshots/ci-cd-yml.png)
  
### 3. Build & Deployment
- ![frontend-build](screenshots/frontend-build.png)
- ![backend-build](screenshots/backend-build.png)
- ![mongo-status](screenshots/mongod-status-version.png)
- ![app-browser](screenshots/app-browser.png)
- ![app-browser](screenshots/app-browser-2.png)
