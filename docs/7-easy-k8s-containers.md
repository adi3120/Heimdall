Perfect — now you’re thinking like a real engineer 🔥

> ❌ Don’t do things manually
> ✅ Define everything once → apply in one shot

---

# 🧠 🎯 Goal

* Clean everything
* Create **ONE YAML file**
* Deploy:

  * namespaces
  * pods
  * services
    👉 in a **single command**

---

# 🧹 Step 1 — Clean Existing K8s Resources

```bash id="9xk8tr"
kubectl delete namespace attacker frontend backend
```

👉 This removes everything inside too

---

# 📦 Step 2 — Create One Unified YAML

Create file:

```bash
vim heimdall-phase1.yaml
```

Paste this 👇

---

```yaml
# ----------------------------
# Namespaces
# ----------------------------
apiVersion: v1
kind: Namespace
metadata:
  name: attacker
---
apiVersion: v1
kind: Namespace
metadata:
  name: frontend
---
apiVersion: v1
kind: Namespace
metadata:
  name: backend

# ----------------------------
# Ubuntu Pod (Frontend)
# ----------------------------
---
apiVersion: v1
kind: Pod
metadata:
  name: ubuntu-pod
  namespace: frontend
  labels:
    app: ubuntu
spec:
  containers:
  - name: ubuntu
    image: ubuntu
    command: ["sleep", "3600"]

# ----------------------------
# Alpine Pod (Backend)
# ----------------------------
---
apiVersion: v1
kind: Pod
metadata:
  name: alpine-pod
  namespace: backend
  labels:
    app: alpine
spec:
  containers:
  - name: alpine
    image: alpine
    command: ["sleep", "3600"]

# ----------------------------
# Kali Pod (Attacker)
# ----------------------------
---
apiVersion: v1
kind: Pod
metadata:
  name: kali-pod
  namespace: attacker
  labels:
    app: kali
spec:
  containers:
  - name: kali
    image: kalilinux/kali-rolling
    command: ["sleep", "3600"]

# ----------------------------
# Ubuntu Service
# ----------------------------
---
apiVersion: v1
kind: Service
metadata:
  name: ubuntu-service
  namespace: frontend
spec:
  selector:
    app: ubuntu
  ports:
    - port: 80
      targetPort: 80

# ----------------------------
# Alpine Service
# ----------------------------
---
apiVersion: v1
kind: Service
metadata:
  name: alpine-service
  namespace: backend
spec:
  selector:
    app: alpine
  ports:
    - port: 80
      targetPort: 80
```

---

# 🚀 Step 3 — Deploy EVERYTHING

```bash id="8j7y1l"
kubectl apply -f heimdall-phase1.yaml
```

---

# 🔍 Step 4 — Verify

```bash id="zmt1b3"
kubectl get pods -A
```

```bash id="p1q0gn"
kubectl get svc -A
```

---

# 🌐 Step 5 — Test (Same as Before)

Enter Kali:

```bash id="p0zv1f"
kubectl exec -n attacker -it kali-pod -- bash
```

Install curl:

```bash id="n8nhg4"
apt update && apt install curl -y
```

---

# ⚠️ Start Server in Ubuntu (once)

```bash id="ok4qrg"
kubectl exec -n frontend -it ubuntu-pod -- bash
```

```bash id="8c9m4x"
apt update && apt install python3 -y
python3 -m http.server 80
```

---

# ✅ Test

```bash id="x8r7gh"
curl ubuntu-service.frontend
```

---

# 🧠 What You Just Did (IMPORTANT)

✔ Infrastructure-as-Code
✔ One command deployment
✔ Clean reproducibility
✔ Real DevOps workflow

---

# 🔥 Huge Upgrade

Before:

```text
run container → test
```

Now:

```text
define system → apply → system exists
```

---

# 💡 Next Level (Coming Soon)

We can improve this further:

* Deployments (auto restart)
* Init containers (auto install tools)
* Pre-baked images (no manual setup)
* Network policies (security 🔐)

---

# 🚀 Next Step

Say:

> **“make it production-grade (no manual steps)”**

and we’ll:

* remove manual apt installs
* auto-run servers
* make pods self-sufficient

You’re now doing real infrastructure engineering 🔥



Perfect 🔥 — now we move from **“lab setup” → “real system design”**

> ❌ No manual `apt install`
> ❌ No manual `exec into pod`
> ✅ Everything auto-starts, self-contained, reproducible

---

# 🧠 🎯 What “Production-Grade” Means Here

We will:

* Use **Deployments** (auto-restart, scalable)
* Use **containers that start services automatically**
* Use **labels + selectors properly**
* Keep everything **declarative**

---

# ⚔️ 🔥 Final Production YAML (Phase 1)

Create:

```bash
vim heimdall-phase1-prod.yaml
```

Paste this 👇

---

```yaml
# ----------------------------
# Namespaces
# ----------------------------
apiVersion: v1
kind: Namespace
metadata:
  name: attacker
---
apiVersion: v1
kind: Namespace
metadata:
  name: frontend
---
apiVersion: v1
kind: Namespace
metadata:
  name: backend

# ----------------------------
# Ubuntu Deployment (Frontend)
# ----------------------------
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: ubuntu-deploy
  namespace: frontend
spec:
  replicas: 1
  selector:
    matchLabels:
      app: ubuntu
  template:
    metadata:
      labels:
        app: ubuntu
    spec:
      containers:
      - name: ubuntu
        image: python:3.11-slim
        command: ["python3", "-m", "http.server", "80"]
        ports:
        - containerPort: 80

# ----------------------------
# Alpine Deployment (Backend)
# ----------------------------
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: alpine-deploy
  namespace: backend
spec:
  replicas: 1
  selector:
    matchLabels:
      app: alpine
  template:
    metadata:
      labels:
        app: alpine
    spec:
      containers:
      - name: alpine
        image: nginx:alpine
        ports:
        - containerPort: 80

# ----------------------------
# Kali Deployment (Attacker)
# ----------------------------
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: kali-deploy
  namespace: attacker
spec:
  replicas: 1
  selector:
    matchLabels:
      app: kali
  template:
    metadata:
      labels:
        app: kali
    spec:
      containers:
      - name: kali
        image: kalilinux/kali-rolling
        command: ["sleep", "3600"]

# ----------------------------
# Services
# ----------------------------
---
apiVersion: v1
kind: Service
metadata:
  name: ubuntu-service
  namespace: frontend
spec:
  selector:
    app: ubuntu
  ports:
    - port: 80
      targetPort: 80

---
apiVersion: v1
kind: Service
metadata:
  name: alpine-service
  namespace: backend
spec:
  selector:
    app: alpine
  ports:
    - port: 80
      targetPort: 80
```

---

# 🚀 Deploy Everything

```bash
kubectl apply -f heimdall-phase1-prod.yaml
```

---

# 🔍 Verify

```bash
kubectl get pods -A
kubectl get svc -A
```

---

# 🧪 Test (No Setup Needed Now)

Enter Kali:

```bash
kubectl exec -n attacker -it deploy/kali-deploy -- bash
```

Install curl (only for testing):

```bash
apt update && apt install curl -y
```

---

## Test frontend:

```bash
curl ubuntu-service.frontend
```

---

## Test backend:

```bash
curl alpine-service.backend
```

---

# 🧠 What Just Improved

| Before       | Now                 |
| ------------ | ------------------- |
| Manual setup | Auto-start services |
| Pods         | Deployments         |
| Fragile      | Self-healing        |
| Interactive  | Declarative         |

---

# 🔥 Big Architectural Upgrade

```mermaid
flowchart LR
    Kali --> Ubuntu
    Ubuntu --> Alpine
```

Now:

* Fully managed by Kubernetes
* Restart if crash
* Easily scalable

---

# 💡 Hidden Win (Important)

You just avoided:

* SSH into containers ❌
* Manual package installs ❌
* Runtime configuration ❌

👉 This is **real production mindset**

---

# 🚀 Next Step (Now It Gets Powerful)

Now we add:

## 🔐 Network Policies (K8s-level security)

Goal:

* ❌ Kali → Alpine (block)
* ✅ Kali → Ubuntu (allow)
* ✅ Ubuntu → Alpine (allow)

---

OR

## ⚔️ Start Heimdall Agent (eBPF)

---

# 🎯 Your Call

Say:

> **“add network policies”**
> OR
> **“start heimdall agent”**

And we go deeper 🔥
