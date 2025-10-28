# 🌐 WordPress + Traefik (Auto SSL) on Docker

A production-ready, secure, and maintainable setup for running WordPress behind Traefik with automatic Let’s Encrypt SSL certificates.

---

## 🚀 Features

- Automatic HTTPS for your domain
- Traefik as reverse proxy & SSL manager
- WordPress + MySQL in isolated Docker network
- Secure by default: no exposed ports on WordPress
- Easy to update image versions via `.env`

---

## 📁 Project Structure
```
wordpress-traefik
├── docker-compose.yml
├── .env.example
├── .gitignore
└── README.md
```
---

## 🛠️ Setup Instructions

### 1. Clone the repo

```bash
git clone https://github.com/jsalehi90/wordpress-traefik.git
cd wordpress-traefik
```

2. Create `.env` file
Copy `.env.example` and fill in your secrets:

```bash
cp .env.example .env
vim .env
```

3. Create Docker network

```bash
docker network create web_net
```

4. Initialize ACME cert storage

```bash
mkdir -p traefik
touch traefik/acme.json
chmod 600 traefik/acme.json
```
5. Start services

```bash
docker compose up -d
```
---

## 🌐 Access Your Site

- WordPress: `https://your-domain.com` (replace with your domain)
- Traefik Dashboard: `https://web.your-domain.com/dashboard/` (Basic Auth required)

---

## 🔐 Security Notes

- All sensitive data is in `.env` — keep it private.
- Traefik dashboard is protected with Basic Auth.
- WordPress does NOT expose any port to the host — all traffic goes through Traefik.

---

## 🔐 How to Generate Basic Auth Password for Traefik

Traefik supports Basic Authentication using the format:

```
username:{SHA}hashed_password
```

To generate a secure SHA-1 hash for your username/password, follow these steps:

---

### 1. Install `htpasswd` (if not installed)

On Ubuntu/Debian:
```bash
sudo apt update && sudo apt install apache2-utils -y
```

On CentOS/RHEL/Fedora:
```bash
sudo yum install httpd-tools -y
# or
sudo dnf install httpd-tools -y
```

---

### 2. Generate the SHA-1 Hash

Run this command to generate the hash (replace `DockerMe` and `yourpassword` with your own):

```bash
htpasswd -nb admin yourpassword | cut -d ":" -f 2
```

✅ Example output:
```
{SHA}Gt3+oW3VTjtIbz6hTTD1VuZDj1w=
```

> 📌 This is the value you should paste into your `.env` file under `TRAEFIK_BASIC_AUTH_PASS`.

---

### 3. Update Your `.env` File

In your `.env` file, set:

```env
TRAEFIK_BASIC_AUTH_USER=admin
TRAEFIK_BASIC_AUTH_PASS={SHA}Gt3+oW3VTjtIbz6hTTD1VuZDj1w=
```

---

## 🔄 Update Image Versions

Edit `.env`:

```env
WORDPRESS_IMAGE=wordpress:6.8
TRAEFIK_IMAGE=traefik:v2.11.0
```

Then:

```bash
docker-compose down
docker-compose up -d --build
```

---