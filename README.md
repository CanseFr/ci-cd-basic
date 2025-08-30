# 🚀 React + Vite + Docker + CI/CD (GitHub Actions) — Deployment Starter Kit

[![CI/CD](https://github.com/YOUR_GITHUB_USERNAME/YOUR_REPO_NAME/actions/workflows/deploy.yml/badge.svg)](https://github.com/YOUR_GITHUB_USERNAME/YOUR_REPO_NAME/actions)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

This project is a complete **starter kit** to deploy a React + Vite application using **Docker**, **GitHub Actions**, and a **VPS** (e.g., IONOS, AWS EC2).  
It's designed to help you learn, understand, and apply professional-grade **CI/CD workflows** without needing Kubernetes.

---

## 📚 What You'll Learn

- How to containerize a Vite app with Docker
- How to create a clean multi-stage Dockerfile
- How to use GitHub Actions for CI/CD
- How to deploy automatically to a VPS
- How to use docker-compose on your server

---

## 📁 Project Structure

```
.
├── .github/workflows/deploy.yml   # GitHub Actions workflow
├── Dockerfile                     # Multi-stage build for React app
├── .dockerignore                  # Files to exclude from Docker build
├── public/                        # Static assets
├── src/                           # React source code
├── package.json                   # Project config and scripts
```

---

## ⚙️ Technologies Used

- [Vite](https://vitejs.dev/) — blazing fast frontend tooling
- [React](https://reactjs.org/) — frontend library
- [Docker](https://www.docker.com/) — containerization
- [GitHub Actions](https://docs.github.com/en/actions) — automation & CI/CD
- [docker-compose](https://docs.docker.com/compose/) — simple service orchestration

---

## 🚀 Getting Started

### 1. Clone the repo

```bash
git clone https://github.com/YOUR_GITHUB_USERNAME/YOUR_REPO_NAME.git
cd YOUR_REPO_NAME
```

### 2. Build & run locally with Docker

```bash
docker build --platform linux/amd64 -t my-app .
docker run -p 3000:3000 my-app
```

Go to [http://localhost:3000](http://localhost:3000) to view the app.

---

## ⚙️ Setup CI/CD Pipeline

### 🔐 Add these secrets to your GitHub repo:

| Name                | Description                     |
|---------------------|---------------------------------|
| `DOCKERHUB_USERNAME`| Docker Hub username              |
| `DOCKERHUB_TOKEN`   | Docker Hub access token          |
| `SERVER_IP`         | VPS IP address                   |
| `SERVER_USER`       | SSH user (e.g. root)             |
| `SERVER_PASSWORD`   | SSH password (or use a PEM key)  |

> You can modify the GitHub Action to use SSH private key if needed.

---

## 🐳 docker-compose on VPS

Place this file on your server (`/home/root/my-app/docker-compose.yml`):

```yaml
version: "3.8"

services:
  frontend:
    image: yourdockerhub/my-app:latest
    restart: always
    ports:
      - "80:3000"
```

---

## 🤖 GitHub Actions Workflow

Path: `.github/workflows/deploy.yml`

```yaml
name: Deploy React App to VPS

on:
  push:
    branches:
      - main

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v3

      - name: Log in to Docker Hub
        uses: docker/login-action@v2
        with:
          username: ${{ secrets.DOCKERHUB_USERNAME }}
          password: ${{ secrets.DOCKERHUB_TOKEN }}

      - name: Build and push Docker image
        uses: docker/build-push-action@v5
        with:
          context: .
          push: true
          tags: yourdockerhub/my-app:latest
          platforms: linux/amd64

      - name: Deploy to VPS
        uses: appleboy/ssh-action@v1.0.3
        with:
          host: ${{ secrets.SERVER_IP }}
          username: ${{ secrets.SERVER_USER }}
          password: ${{ secrets.SERVER_PASSWORD }}
          script: |
            cd /home/root/my-app
            docker pull yourdockerhub/my-app:latest
            docker-compose down
            docker-compose up -d
```

---

## ✅ Result

After every push to `main`:
1. Your image is built and pushed to Docker Hub.
2. Your VPS pulls the new image.
3. The container is restarted automatically.

Visit: `http://<your-server-ip>`

---

## 📄 License

This project is licensed under the MIT License.