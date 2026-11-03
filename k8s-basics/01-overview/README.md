# 01 · Setup + Kubernetes Overview
Date: 2026-11-02 · Course: KodeKloud – Kubernetes for the Absolute Beginners

## What I learned

### Containers
- A container packages an app with everything it needs (code, libraries, runtime)
  so it runs the same on any machine. No more "works on my machine".
- Containers share the host's OS kernel, so they are much lighter and faster to
  start than virtual machines.
- Docker is the most common tool to build and run containers. An **image** is
  the template; a **container** is a running instance of that image.

### Container orchestration
- Running one container is easy. Running hundreds across many servers is not:
  what if one crashes, a server dies, or traffic suddenly doubles?
- Orchestration automates this: it places containers on servers, restarts them
  when they fail, scales them up and down, and connects them over the network.
- Kubernetes (K8s) is the most popular orchestrator. Alternatives include
  Docker Swarm and Apache Mesos.

### Kubernetes architecture
- **Node:** a machine (physical or virtual) where containers run.
- **Cluster:** a group of nodes working together, so if one node fails the app
  keeps running on the others.
- **Control plane (master):** the "brain" that manages the cluster.
- **Worker nodes:** the machines that actually run my apps.

| Component | Where it runs | What it does (one line) |
|---|---|---|
| kube-apiserver | Control plane | Front door of the cluster. Every command (including kubectl) goes through it. |
| etcd | Control plane | Key-value database that stores the entire state of the cluster. |
| kube-scheduler | Control plane | Decides which node a new container (pod) should run on. |
| kube-controller-manager | Control plane | Constantly checks actual state vs desired state and fixes differences (e.g. restarts failed pods). |
| kubelet | Every worker node | Agent that makes sure the containers assigned to its node are running. |
| container runtime | Every node | Software that actually runs containers (e.g. containerd). |
| kube-proxy | Every worker node | Handles networking rules so traffic reaches the right containers. |

- **kubectl** is the command-line tool I use to talk to the cluster through the
  API server.

### How I think about it (API-gateway analogy)
The kube-apiserver is like an API gateway for the whole cluster: one entry
point, authenticates every request, then hands work to the right component.

## Self-check questions

**Q: What problem does container orchestration solve?**
A: Managing many containers across many machines by hand is impossible to do
reliably. Orchestration automates deployment, scaling, self-healing (restarting
failed containers) and networking, so apps stay available without manual work.

**Q: Name each control-plane component and what it does.**
A:
- kube-apiserver: entry point for all requests to the cluster
- etcd: stores all cluster data
- kube-scheduler: picks the node for each new pod
- kube-controller-manager: keeps the actual state matching the desired state

## Commands I used
```bash
brew install kubectl kind         # install the Kubernetes CLI and kind
kind create cluster               # create a local practice cluster
kubectl get nodes                 # list the nodes in the cluster
kubectl cluster-info              # show where the control plane is running
kind delete cluster               # remove the cluster when done
```

## What confused me / mistakes I made
- **Why does Kubernetes need etcd?**
  I didn't understand why a separate database was needed.
  What I understood after reading more:
  - etcd is the cluster's memory: it stores the desired and current state of
    everything (pods, deployments, configs, secrets).
  - Kubernetes constantly compares desired vs actual state, so it needs one
    reliable source of truth.
  - etcd is consistent across multiple control-plane nodes (Raft), lets
    components watch for changes instantly, and survives node failures.
  - Only the kube-apiserver talks to etcd directly.
  - Similar to how Kong stores routes and plugins in Postgres.
  - If etcd is lost, the cluster loses its state, so backups matter
    (comes up later in CKA: etcd backup and restore).

## Next
Kubernetes Concepts and YAML.