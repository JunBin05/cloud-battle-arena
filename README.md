# ☁️ GDGoC UM Cloud Workshop: Build Your Own Agar.io Clone (UM Arena)

Welcome to the UM Arena Cloud Workshop! In this hands-on session, you will deploy a full-stack multiplayer game. You will learn the complete development lifecycle: testing locally, using Docker and Google Cloud Platform (GCP) to host your backend game server, and using Firebase to host your web client.

By the end of this guide, you will have your very own game arena and the **"God Mode"** powers to control it.

---

## 🛠️ Prerequisites

Before we start, ensure you have the following installed:

* Google Cloud Account (with credits)
* Google Cloud CLI (`gcloud`)
* Docker Desktop (must be running)
* Node.js
* Git
* Firebase CLI

Install Firebase CLI:

```bash
npm install -g firebase-tools
```

---

## 💻 Part 1: Local Testing (The Dry Run)

### 1. Clone the Repository

```bash
git clone <YOUR_GITHUB_REPO_URL>
cd <YOUR_REPO_NAME>/Cigar
```

### 2. Build and Run Backend Locally

```bash
docker build -t agar-server-local .
docker run -d -it -p 8080:8080 --name my-local-server agar-server-local
```

### 3. Test Frontend Connection

* Go to `Cigar/www`
* Open `index.html` in Chrome
* Enter server IP: `127.0.0.1:8080`
* Click **Play**

If you spawn successfully → you're ready for the cloud 🚀

---

## 🐳 Part 2: Build & Push Backend to GCP

### 1. Create Artifact Registry

* Go to **Artifact Registry** in GCP
* Create repository:

  * Name: `agar-repo`
  * Format: Docker
  * Region: e.g. `asia-southeast1`

### 2. Build & Push Image

```bash
gcloud auth configure-docker asia-southeast1-docker.pkg.dev

docker build -t asia-southeast1-docker.pkg.dev/<PROJECT_ID>/agar-repo/agar-server:v1 .

docker push asia-southeast1-docker.pkg.dev/<PROJECT_ID>/agar-repo/agar-server:v1
```

---

## 🚀 Part 3: Deploy Backend (Compute Engine)

### 1. Create VM & Deploy Container

* Go to **Compute Engine → VM Instances → Create Instance**
* Name: `agar-arena-vm`
* Machine type: `e2-micro` or `e2-medium`

#### Container Settings

* Click **Deploy Container**
* Image:

```
asia-southeast1-docker.pkg.dev/<PROJECT_ID>/agar-repo/agar-server:v1
```

Enable:

* Allocate a pseudo-TTY (-t)
* Keep STDIN open (-i)

#### Firewall

* Allow HTTP
* Allow HTTPS

Click **Create**

---

### 2. Open Port 8080

* Go to **VPC Network → Firewall**
* Create rule:

  * Name: `allow-agar-8080`
  * Source: `0.0.0.0/0`
  * Protocol: TCP `8080`

---

## 🌐 Part 4: Deploy Frontend (Firebase)

### 1. Initialize Firebase

```bash
firebase login
firebase init hosting
```

Use these settings:

* Public directory: `www`
* Single-page app: `No`
* GitHub deploys: `No`
* Overwrite index.html: **No (IMPORTANT)**

### 2. Deploy

```bash
firebase deploy
```

You’ll get a URL like:

```
https://your-project.web.app
```

---

## 🎮 Part 5: Play the Game

### ⚠️ Enable Insecure Content (Important)

Because Firebase uses HTTPS and your backend uses `ws://`, Chrome blocks it initially.

Steps:

1. Open your Firebase URL
2. Click 🔒 icon
3. Go to **Site Settings**
4. Allow **Insecure Content**
5. Refresh page

### Connect to Server

* Enter your VM External IP:

```
<YOUR_VM_IP>:8080
```

* Enter nickname → Click **Play**

---

## ⚡ Bonus: God Mode (Admin Console)

### 1. SSH into VM

Go to **Compute Engine → VM Instances → SSH**

### 2. Find Container

```bash
sudo docker ps
```

### 3. Attach to Game Server

```bash
sudo docker attach <CONTAINER_ID_OR_NAME>
```

Press Enter if blank.

---

### 🎛️ Admin Commands

```bash
addbot 1 20        # Spawn 20 bots
setting pelletMinSize 50   # Giant food
mass all 1000      # Everyone becomes huge
killall 1          # Reset map
```

---

### ⚠️ Exit Safely

**DO NOT press Ctrl + C** (kills server)

Instead:

```
Ctrl + P → Ctrl + Q
```

---

## 🎉 You're Done!

You now have your own cloud-hosted Agar.io-style multiplayer game. Invite friends, tweak physics, and rule your arena like a digital overlord 👑

Happy hacking!
