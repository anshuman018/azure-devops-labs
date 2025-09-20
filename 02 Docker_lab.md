# Docker Netflix Clone Deployment Lab

## Objective
Deploy Netflix clone using Docker containers, create custom Docker image, and push to Docker Hub & Azure Container Registry (ACR).

---

## Step 1: Create VM and Connect

1. **Create Ubuntu VM** (same as Part 1)
   - Use **Ubuntu Server 20.04 LTS**
   - Size: **B1s** (free tier)
   - Open ports: **22, 80, 443**
   - Download **.pem** file

2. **Connect via SSH**
   ```bash
   ssh -i key.pem azureuser@<VM_PUBLIC_IP>
   ```

---

## Step 2: Install Docker

Run these commands on your VM:

```bash
# Update system
sudo apt update
sudo apt upgrade -y

# Install Docker
sudo apt install -y docker.io

# Start and enable Docker
sudo systemctl start docker
sudo systemctl enable docker

# Verify Docker installation
docker --version

# Test Docker with hello-world
sudo docker run hello-world
```

### Allow Docker without sudo
```bash
# Add user to docker group
sudo usermod -aG docker $USER

# Apply group changes immediately
newgrp docker
```

---

## Step 3: Clone Netflix Repository

```bash
# Clone the repository
git clone https://github.com/anshuman018/Netflix-Static.git

# Navigate to project directory
cd Netflix-Static

# List files to verify
ls
```

---

## Step 4: Create Dockerfile

1. **Create Dockerfile**
   ```bash
   nano Dockerfile
   ```

2. **Paste this content:**
   ```dockerfile
   FROM nginx:alpine
   RUN rm -rf /usr/share/nginx/html/*
   COPY . /usr/share/nginx/html
   EXPOSE 80
   CMD ["nginx", "-g", "daemon off;"]
   ```

3. **Save and exit**
   - Press `Ctrl + X`
   - Press `Y` (Yes)
   - Press `Enter`

4. **Verify Dockerfile**
   ```bash
   # Check file exists
   ls

   # Verify content
   cat Dockerfile
   ```

---

## Step 5: Build Docker Image

1. **Create Docker Hub Account**
   - Go to [hub.docker.com](https://hub.docker.com)
   - Sign up and note your **username**

2. **Build Docker Image**
   ```bash
   # Replace 'yourusername' with your Docker Hub username
   docker build -t yourusername/netflix-clone:latest .
   ```

3. **Verify Image**
   ```bash
   docker images
   ```
   You should see your image listed.

---

## Step 6: Run Container

1. **Start Container**
   ```bash
   # Replace with your actual image name
   docker run -d -p 80:80 yourusername/netflix-clone:latest
   ```

2. **Verify Container Running**
   ```bash
   docker ps
   ```

3. **Test in Browser**
   - Open browser
   - Go to: `http://<VM_PUBLIC_IP>` (NOT https)
   - Netflix clone website should appear

---

## Step 7: Push to Docker Hub

1. **Login to Docker Hub**
   ```bash
   # Replace with your username
   docker login -u yourusername
   ```
   Enter your password when prompted.

2. **Push Image**
   ```bash
   # Replace with your image name
   docker push yourusername/netflix-clone:latest
   ```

3. **Verify on Docker Hub**
   - Go to [hub.docker.com](https://hub.docker.com)
   - Check your repositories
   - Your image should be visible

---

## Step 8: Push to Azure Container Registry (ACR)

### 8.1 Create ACR

1. **Azure Portal** → **Create Resource** → **Container Registry**
2. Settings:
   - **Name**: `netflixacr[yourname]` (must be unique)
   - **SKU**: Basic
   - **Admin user**: Enable

### 8.2 Get ACR Credentials

1. Go to your ACR → **Access keys**
2. Note:
   - **Login server**: `netflixacr[yourname].azurecr.io`
   - **Username**: (admin username)
   - **Password**: (admin password)

### 8.3 Tag and Push to ACR

```bash
# Login to ACR
docker login netflixacr[yourname].azurecr.io

# Tag image for ACR
docker tag yourusername/netflix-clone:latest netflixacr[yourname].azurecr.io/netflix-clone:latest

# Push to ACR
docker push netflixacr[yourname].azurecr.io/netflix-clone:latest
```

### 8.4 Verify in ACR

1. Azure Portal → Your ACR → **Repositories**
2. You should see `netflix-clone` repository

---

## Verification Checklist

- [ ] ✅ Docker installed and running
- [ ] ✅ Netflix repository cloned
- [ ] ✅ Dockerfile created correctly
- [ ] ✅ Docker image built successfully
- [ ] ✅ Container running on port 80
- [ ] ✅ Website accessible via VM IP
- [ ] ✅ Image pushed to Docker Hub
- [ ] ✅ Image pushed to Azure ACR

---

## Troubleshooting

**Docker permission denied?**
```bash
sudo usermod -aG docker $USER
newgrp docker
```

**Build fails?**
- Check Dockerfile content with `cat Dockerfile`
- Ensure you're in Netflix-Static directory

**Container not accessible?**
- Use `http://` not `https://`
- Check VM security group allows port 80
- Verify container is running: `docker ps`

**Push fails?**
- Login first: `docker login`
- Check image name matches exactly

---

## Docker Commands Reference

```bash
# Build image
docker build -t imagename .

# List images
docker images

# Run container
docker run -d -p 80:80 imagename

# List running containers
docker ps

# Stop container
docker stop container_id

# Remove container
docker rm container_id

# Remove image
docker rmi imagename
```

---

## Submission

Provide:
1. **Docker Hub Username**: `_________________`
2. **Docker Hub Repository URL**: `_________________`
3. **ACR Name**: `_________________`
4. **VM Public IP**: `_________________`
5. **Screenshot**: Website running via Docker
6. **Screenshot**: Docker Hub repository
7. **Screenshot**: ACR repository
