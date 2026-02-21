# Docker Setup & Architecture Understanding

## 1. Why Did Docker Engine Initially Fail?

Docker Desktop requires a Linux kernel to run containers.

On Windows:

Docker Desktop
→ Uses WSL2
→ WSL2 uses Hyper-V
→ Hyper-V requires CPU virtualization (SVM for AMD)

When SVM was disabled in BIOS:

CPU → Virtualization not supported  
Hyper-V → Cannot start  
WSL2 → Cannot boot Linux kernel  
Docker Engine → Fails to start  

After enabling SVM in BIOS, Hyper-V was able to initialize, WSL2 started properly, and Docker Engine ran successfully.

---

## 2. What is Virtualization?

Virtualization is a hardware feature that allows a CPU to run multiple isolated operating systems simultaneously.

Modern CPUs provide virtualization extensions:

- Intel → VT-x
- AMD → SVM

These extensions allow:

- Running multiple OS kernels
- Isolating memory between systems
- Supporting hypervisors (Hyper-V, VMware, KVM)

Virtualization is not just "creating VMs".
It is hardware-level isolation support built into the CPU.

---

## 3. Why is WSL2 Required for Docker on Windows?

Docker runs Linux containers.

Windows kernel ≠ Linux kernel.

Therefore:

Docker Desktop installs WSL2
→ WSL2 runs a real Linux kernel
→ Docker Engine runs inside that Linux environment
→ Containers run inside that Linux kernel

Without WSL2 (or Hyper-V backend), Windows cannot run Linux containers.

---

## 4. Docker Client vs Docker Server

When running:

docker run nginx

You are using the Docker CLI (Client).

The client:
- Sends commands via REST API
- Does NOT run containers directly

The Docker Daemon (Server):
- Pulls images
- Creates containers
- Manages networking
- Manages storage
- Controls lifecycle

Architecture:

CLI (Windows)
→ Docker Engine (Linux in WSL2)
→ containerd
→ runc
→ Container

From `docker version`:

Client: windows/amd64  
Server: linux/amd64  

This confirms containers run on Linux, not Windows.

---

## 5. What Does `-d` Do?

-d = Detached mode

Runs container in background.

Without -d:
Terminal is attached to container process.

With -d:
Docker daemon manages container independently.

---

## 6. What Does `-p 8080:80` Mean?

Syntax:

HOST_PORT:CONTAINER_PORT

8080 → Port on Windows host  
80 → Port inside container  

Traffic flow:

Browser
→ Windows port 8080
→ Docker NAT rule
→ Container port 80
→ nginx process

This is Network Address Translation (NAT).

---

## 7. Mistakes Made

1. Virtualization (SVM) disabled in BIOS.
2. Cloned Git repository inside:

C:\Windows\System32

System directories should not be used for development.

Correct practice:

C:\Users\<username>\

## Full Stack Architecture

Hardware (AMD CPU with SVM enabled)
        ↓
Hyper-V (Windows Hypervisor)
        ↓
WSL2 (Lightweight Linux VM)
        ↓
Docker Engine (dockerd)
        ↓
containerd
        ↓
runc
        ↓
Linux Container (nginx)