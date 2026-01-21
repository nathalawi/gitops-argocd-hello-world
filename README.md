# GitOps Live Demo – Kubernetes with Argo CD

This repository contains the **live demo** used in the **TechTalk With Nathan** GitOps video.
It demonstrates how to deploy and manage applications on Kubernetes using **GitOps principles**, with **Git as the single source of truth** and **Argo CD** as the GitOps controller.

---

## 📌 What This Demo Shows

* What GitOps is in practice
* How Git becomes the **only interface** for deploying to Kubernetes
* How Argo CD continuously reconciles the cluster state with Git
* How changes in Git automatically update Kubernetes
* How GitOps enables auditability, rollback, and consistency

The key takeaway:

> **Change Git → Kubernetes updates automatically**

---

## GitOps in One Sentence

GitOps is a set of practices where **Git defines the desired state** of your system, and an automated controller ensures the **actual state always matches Git**.

---

## Architecture Overview

```
Git Repository (desired state)
        ↓
   Argo CD (GitOps controller)
        ↓
Kubernetes Cluster (actual state)
```

* Git contains declarative Kubernetes manifests (YAML)
* Argo CD runs inside the cluster
* Argo CD pulls from Git and reconciles continuously
* No manual `kubectl apply` is required

---

## Repository Structure

```
apps/
  hello-world/
    deployment.yaml
    service.yaml
```

* `apps/hello-world/deployment.yaml` – Defines the desired state of the application
* `apps/hello-world/service.yaml` – Exposes the application inside the cluster

This structure is intentionally simple to focus on **GitOps fundamentals**.

---

## Prerequisites

You will need:

* Kubernetes cluster (Minikube, kind, or any Kubernetes cluster)
* `kubectl`
* `git`
* GitHub account (or any Git provider)
* Internet access (to install Argo CD)

This demo was recorded using **Minikube**, but works the same on any cluster.

---

## Getting Started

### 1️⃣ Start Kubernetes (example with Minikube)

```bash
minikube start
```

Verify access:

```bash
kubectl get nodes
```

---

### 2️⃣ Install Argo CD

```bash
kubectl create namespace argocd
kubectl apply -n argocd \
  -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

Wait until all pods are running:

```bash
kubectl get pods -n argocd
```

---

### 3️⃣ Access the Argo CD UI (local demo)

```bash
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

Then open:

```
https://localhost:8080
```

Retrieve the initial admin password:

```bash
kubectl get secret argocd-initial-admin-secret \
  -n argocd \
  -o jsonpath="{.data.password}" | base64 -d
```

---

### 4️⃣ Create an Argo CD Application

In the Argo CD UI:

* Repository URL: this Git repository
* Path: `apps/hello-world`
* Destination cluster: `https://kubernetes.default.svc`
* Namespace: `default`
* Sync policy: **Manual or Automatic**

This tells Argo CD **what to watch** and **where to deploy it**.

---

## The GitOps Moment (Core Demo)

1. Open `deployment.yaml`
2. Change the number of replicas:

```yaml
replicas: 3
```

3. Commit and push the change:

```bash
git commit -am "Scale hello-world to 3 replicas"
git push
```

4. Watch Kubernetes update automatically:

```bash
kubectl get pods
```

You should see new Pods created **without running any kubectl commands**.

This is GitOps in action.

---

## Rollback Example

To rollback, simply revert the Git change:

```bash
git revert HEAD
git push
```

Argo CD will automatically reconcile the cluster back to the previous state.

---

## Why This Is More Secure

* Humans do not need direct access to the cluster
* All changes go through Git (PRs, reviews, history)
* Clear audit trail of **who changed what and when**
* Reduced risk of configuration drift

---

## 📈 When GitOps Makes Sense

GitOps works best when:

* You use Kubernetes
* You have multiple environments
* You work in teams
* You care about auditability and consistency

It may be overkill for small experiments, but it shines at scale.

---

## Related Content

This repository accompanies the **GitOps demo video** on the **TechTalk With Nathan** YouTube channel.

Upcoming topics:

* GitOps + Ingress
* GitOps with HTTPS and cert-manager
* GitOps for multi-environment setups

---

## Cleanup

To remove the demo:

```bash
kubectl delete namespace argocd
kubectl delete deployment hello-world
kubectl delete service hello-world
```

---

## 📄 License

This demo is provided for **educational purposes**.

Use it, modify it, and adapt it for your own learning or demos.
