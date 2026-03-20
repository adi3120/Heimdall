Perfect — this is the best place to start.
You already understand containers + networking, so Kubernetes will **click much faster now**.

We’ll go **slow, practical, no theory overload**.

---

# 🧠 🧱 What You Need to Know (ONLY THIS)

Before we touch commands:

| Thing   | Meaning                          |
| ------- | -------------------------------- |
| Cluster | Your whole K8s system            |
| Node    | Machine running stuff            |
| Pod     | Your container (Ubuntu/Kali/etc) |
| Service | How pods talk to each other      |

👉 That’s enough to start.

---

# 🎯 Goal

Recreate your Podman setup inside Kubernetes:

* Kali → attacker
* Ubuntu → service
* Alpine → backend

---

# ⚙️ Step 1 — Install & Start Kubernetes

## On Mac (easy way)

```bash
brew install minikube kubectl
```

---

## Start cluster

```bash
minikube start --cni=cilium
```

---

## Verify

```bash
kubectl get nodes
```

Expected:

```text
NAME       STATUS   ROLES
minikube   Ready    control-plane
```

👉 If you see **Ready**, you’re good.

---

# 🧩 Step 2 — Create Isolation (Namespaces)

Think of namespaces like folders.

```bash
kubectl create namespace attacker
kubectl create namespace frontend
kubectl create namespace backend
```

---

# 📦 Step 3 — Deploy Pods (Your Containers)

We’ll recreate your 3 containers.

---

## 🐧 Ubuntu Pod

```bash
kubectl run ubuntu-pod \
  --image=ubuntu \
  --namespace=frontend \
  --command -- sleep 3600
```

---

## 🏔️ Alpine Pod

```bash
kubectl run alpine-pod \
  --image=alpine \
  --namespace=backend \
  --command -- sleep 3600
```

---

## 🐱‍💻 Kali Pod

```bash
kubectl run kali-pod \
  --image=kalilinux/kali-rolling \
  --namespace=attacker \
  --command -- sleep 3600
```

---

# 🔍 Step 4 — Verify Pods

```bash
kubectl get pods -A
```

You should see all 3 pods running.

---

# 🌐 Step 5 — Get Pod IPs

```bash
kubectl get pods -A -o wide
```

👉 You’ll see something like:

```text
ubuntu-pod   10.244.x.x
alpine-pod   10.244.x.x
kali-pod     10.244.x.x
```

---

# 🔥 This is EXACTLY like your Podman network

But now:

* managed by Kubernetes
* scalable
* controlled

---

# 🧪 Step 6 — Test Connectivity

Enter Kali:

```bash
kubectl exec -n attacker -it kali-pod -- bash
```

Install tools:

```bash
apt update && apt install curl iproute2 -y
```

---

## Test Ubuntu (using IP)

```bash
curl <ubuntu-pod-ip>
```

👉 It will fail (no server yet — expected)

---

# 🔥 Step 7 — Start Server in Ubuntu

```bash
kubectl exec -n frontend -it ubuntu-pod -- bash
```

Inside:

```bash
apt update && apt install python3 -y
python3 -m http.server 80
```

---

# ✅ Test Again (from Kali)

```bash
curl <ubuntu-pod-ip>
```

👉 You should see HTML (same as Podman earlier)

---

# 🧠 What You Just Built

✔ Pods instead of containers
✔ Cluster-managed network
✔ Real inter-pod communication
✔ Same topology as before

---

# ⚔️ Important Difference You’ll Notice

| Podman              | Kubernetes    |
| ------------------- | ------------- |
| Name-based access   | ❌ Not default |
| IP-based access     | ✅ Works       |
| Service abstraction | ⭐ Coming next |

---

# 🚀 Next Step (VERY IMPORTANT)

Right now you used IPs (not ideal)

👉 Next we will:

> **Create Services (DNS-based communication)**

So you can do:

```bash
curl ubuntu-service.frontend
```

---

# 🧠 Don’t Worry

You’re doing perfectly:

* You understood containers ✔
* You understood networking ✔
* Now you’re learning orchestration ✔

---

# 🚀 Say Next

> **“pods running, add service”**

and we’ll:

* add DNS
* clean communication
* move closer to Heimdall integration 🔥
