# ☁️ Raspberry Pi Personal Cloud

A self-hosted personal cloud built using a **Raspberry Pi 4**, **Docker**, and **Nextcloud**.
This project was created to better understand containerization, Docker Compose, multi-container applications, persistent storage, and self-hosting while solving a real problem: running out of Google Drive storage due to years of accumulated emails and files.
Instead of relying on third-party cloud providers, the Raspberry Pi hosts a personal cloud accessible through a web browser, with all data stored locally on an external SSD.

---

## Features

-  Self hosted cloud storage
-  Multi user support
-  User authentication
-  File upload and download
-  Folder management
-  Synchronization
-  Permanent storage using Docker volumes
-  Automatic container restart after Raspberry Pi reboot
-  Accessible only from a device on the local network

---

## Tech Stack

### Hardware

- Raspberry Pi 4
- External M.2 SSD (through USB 3.0 port)
- Raspberry Pi OS Lite (64-bit)

### Software

- Docker
- Docker Compose
- Nextcloud
- MariaDB
- Redis

---

# Architecture

```
                      Browser
                          │
                          │
                          ▼
                Nextcloud Container
            (Apache + PHP + Nextcloud)
                   │              │
                   │              │
                   ▼              ▼
         MariaDB Container   Redis Container
          (Metadata DB)      (Cache & Locks)
                   │
                   ▼
             Docker Volumes
                   │
                   ▼
             External M.2 SSD
```

---

# Container Responsibilities

## Nextcloud

The primary application that provides:

- Web interface
- User authentication
- File management
- File synchronization
- Sharing capabilities

It communicates with MariaDB to store metadata and Redis for caching and file locking.

---

## MariaDB

Stores application metadata including:

- User accounts
- Password hashes
- File metadata
- Folder structure
- Permissions
- Application settings

**Note:** MariaDB does NOT store actual user data, instead only the user's metadata

---

## Redis

Acts as an in-memory cache and locking service.

Responsibilities include:

- File locking
- Faster metadata access
- Improved application performance
- Handling concurrent file access

---

## Docker Volumes

Docker volumes provide persistent storage.

Even if containers are recreated or updated, user files and database data remain intact because they are stored outside the containers.

---

# Project Structure

```
.
├── docker-compose.yml
├── .env.example
├── .gitignore
├── README.md
└── screenshots/
```

---

# What I Learned

Through this project, I gained practical experience with:

- Docker Images vs Containers
- Docker Compose
- Multi-container architecture
- Docker networking
- Named volumes
- Environment variables
- Service orchestration
- Self-hosting
- Linux server management
- SSH
- Persistent storage

---


# Getting Started
## Pi Setup
- Install **Raspberry Pi Imager**.
- Burn **Raspberry Pi OS Lite (64-bit)** into your secondary storage device.
  - While customizing the install, make sure to **enable SSH** and choose your Wifi so that the imager implicitly deals with the tedious work of configuring the network for the first time.
- Plug that storage device into the Raspberry Pi 3/4 and power the Pi. Also, once you have powered it on for the first time, make your Raspberry Pi's IP Address static through your router settings. Since usually IP addresses aren't static by default and change every 24 hours, you'd have to see your Pi's IP every time you want to access it (since it will change).
- After setting the IP static, go to your laptop's PowerShell and perform SSH Handshake using the command `ssh <username>@<ip_address_of_pi>` . It will then prompt you for the password. Enter the password, and then you will gain remote access to the Raspberry Pi through your laptop.
- Now, you will clone the repository.

Clone the repository:

```bash
git clone https://github.com/harison-ford/raspberry-pi-nextcloud.git
cd raspberry-pi-nextcloud
```

Create a `.env` file using the provided example:

```bash
cp .env.example .env
```

Edit the values inside `.env` according to your environment.

Start the application:

```bash
docker compose up -d
```

Open your browser and navigate to:

```
http://<raspberry-pi-ip>:8080
```

Complete the Nextcloud setup by creating an administrator account.

---

# Inspiration

This project was inspired by the following article:

https://www.makeuseof.com/raspberry-pi-became-personal-cloud-one-install/
