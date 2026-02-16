# 🔥 Docker vs ContainerD – A Complete CKA Guide

As Kubernetes evolved, so did the container runtime ecosystem. While Docker once dominated the container landscape, modern Kubernetes clusters primarily use ContainerD or other CRI-compatible runtimes.

Understanding the relationship between Docker, ContainerD, CRI, and the associated CLI tools is critical for:

- 🏅 **CKA certification**
- 💻 **DevOps interviews**
- 🛠️ **Kubernetes troubleshooting**

Let's break this down clearly.

---

## 🧭 The Evolution of Container Runtimes

- In the early days, Docker simplified container usage with an intuitive CLI and developer-friendly tooling.
- Kubernetes was originally built to orchestrate Docker containers directly.
- As alternatives like rkt and CRI-O emerged, Kubernetes needed a standardized way to communicate with *any* runtime.

**Enter:**

### 🔌 Container Runtime Interface (CRI)

- The CRI is a plugin interface allowing Kubernetes to interact with any container runtime.
- Ensures compliance with:
  - OCI Image Specification
  - OCI Runtime Specification
- Standardized communication means Kubernetes can support multiple runtimes without tight coupling.

---

## 🧱 Docker's Architecture (Before CRI Integration)

- Docker includes multiple components:
  - Docker CLI
  - Docker API
  - Image build tools
  - Volume & networking management
  - **containerd**
  - **runc** (actual low-level runtime)
- Docker was built before CRI existed, so it was **not natively CRI-compatible**.

### 🩹 Docker Shim (Deprecated)
- Dockershim acted as a translation layer:
  ```
  Kubernetes ↔ Dockershim ↔ Docker ↔ containerd ↔ runc
  ```
- This added complexity and maintenance overhead.

---

## 🚀 ContainerD Emerges

- Cloud Native Computing Foundation maintains ContainerD independently.
- Originally part of Docker, now it's a standalone, CRI-compatible runtime.

**Key Points:**
- CRI-compatible (no shim required)
- Lightweight, production-grade
- Used directly by Kubernetes

---

## ❗ Kubernetes 1.24+ – Docker Runtime Removed

- **Dockershim removed** from Kubernetes 1.24 onward.
- Docker Engine is no longer supported as a runtime.
- **Supported:** ContainerD, CRI-O, and other CRI runtimes.

> ⚠️ **Clarification:**  
> **Docker images still work** (OCI-compliant). The change only affects the runtime layer, not the image format.

---

## 🏗 Architecture Relationship: Before and After

**Before Kubernetes 1.24:**
```
Kubernetes → Dockershim → Docker → containerd → runc
```

**After Kubernetes 1.24:**
```
Kubernetes → containerd → runc
```
_Much simpler and cleaner!_

---

## 🔍 CLI Tools for ContainerD & Kubernetes

Three major CLI tools to know for the CKA:

### 1️⃣ CTR – Low-Level Debugging Tool

- **Bundled with ContainerD**
- Use cases: Debugging, runtime testing
- Example:
  ```bash
  ctr images pull docker.io/library/redis:alpine
  ctr run docker.io/library/redis:alpine redis
  ```
- ⚠️ *Not recommended for production workflows. Limited features. Designed for debugging.*

---

### 2️⃣ NerdCTL – Docker-Like CLI for ContainerD

- **Provides Docker-like experience** on ContainerD
- Example:
  ```bash
  nerdctl run --name redis redis:alpine
  ```
- Additional features:
  - Lazy image pulling
  - Encrypted images
  - P2P distribution, image signing
- **Best choice for daily ContainerD usage.**

---

### 3️⃣ CRICTL – Kubernetes Runtime Debugging Tool

- **Maintained by Kubernetes community**
- Works with any CRI-compatible runtime: containerd, CRI-O, cri-dockerd
- Use cases: Pull/list images, view logs, inspect pods, debug runtime issues
- Examples:
  ```bash
  crictl pull busybox
  crictl images
  crictl ps -a
  ```
- ⚠️ *Containers created via crictl manually may be removed by kubelet (not part of a Pod).*

---

## 🔄 Kubernetes Runtime Endpoint Changes

**Before Kubernetes 1.24:**
- `dockershim.sock`
- `containerd.sock`
- `crio.sock`
- `cri-dockerd.sock`

**After 1.24:**
- `dockershim` removed
- `cri-dockerd` introduced
- Explicit runtime endpoint config needed:
  ```bash
  crictl --runtime-endpoint <endpoint>
  export CONTAINER_RUNTIME_ENDPOINT=<endpoint>
  ```

---

## 📊 Tool Comparison Summary

| Tool | Maintained By | Purpose | Recommended For |
|------|---------------|---------|-----------------|
| **ctr** | ContainerD | Low-level debugging | Runtime testing |
| **nerdctl** | ContainerD community | Docker-like CLI | Daily usage |
| **crictl** | Kubernetes community | CRI runtime debug | Kubernetes troubleshooting |

---

## 🧠 Final Understanding

- **Docker:** Full container platform
- **ContainerD:** Lightweight CRI-compatible runtime
- **Kubernetes:** Now talks directly to ContainerD
- **nerdctl:** Replaces Docker CLI in ContainerD setups
- **crictl:** Used for runtime debugging in Kubernetes

---

## 📄 Docker vs ContainerD – Interview Cheat Sheet

### 🧠 Core Concepts

- Docker = Container platform
- ContainerD = CRI-compatible runtime
- Kubernetes uses CRI to communicate with runtimes
- Dockershim removed in Kubernetes 1.24
- Docker images remain OCI-compatible

---

## 🔥 Most Asked Interview Questions

### 1️⃣ Why was Docker removed in Kubernetes 1.24?

Because it required dockershim (added complexity, maintenance overhead). ContainerD natively supports CRI.

### 2️⃣ What is CRI?

Container Runtime Interface – allows Kubernetes to communicate with any container runtime.

### 3️⃣ Does Kubernetes still support Docker images?

Yes. OCI-compliant images continue to work perfectly.

### 4️⃣ Docker vs ContainerD?

- **Docker:** Full platform with image build, volume/networking management, CLI
- **ContainerD:** Lightweight, CRI-compatible, focused on container lifecycle

### 5️⃣ When to use ctr vs nerdctl vs crictl?

- **ctr** → Debugging containerd internals
- **nerdctl** → Day-to-day container usage
- **crictl** → Kubernetes runtime debugging

### 6️⃣ What happens if you create a container with crictl manually?

It may be removed by kubelet if not part of a Kubernetes-managed Pod.

### 7️⃣ What is runc?

Low-level OCI runtime used by containerd and Docker for executing containers.

### 8️⃣ What is cri-dockerd?

A replacement for dockershim that allows Docker Engine to work in Kubernetes via CRI.

---

## 🔴 Advanced Scenario Questions

- ❓ How do you troubleshoot runtime errors in Kubernetes?
- ❓ How do you check which runtime Kubernetes is using?
- ❓ How do you configure runtime endpoint in kubelet?
- ❓ How do you debug image pull failures?
- ❓ How do you check container runtime logs?

---

## 🎯 Key Takeaways for CKA Exam

1. **Dockershim is gone** after K8s 1.24
2. **ContainerD is the default** in modern K8s
3. **Docker images still work** (OCI format)
4. **Use crictl** for runtime debugging in Kubernetes
5. **Use nerdctl** for Docker-like container operations
6. **ctr is for debugging** only, not production

---

**Last Updated:** 2026-02-16