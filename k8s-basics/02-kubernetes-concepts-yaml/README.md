# 02 · Kubernetes Concepts + YAML
Date: 2026-11-03 · Course: KodeKloud – Kubernetes for the Absolute Beginners

## What I learned

### Pods
- A **pod** is the smallest thing Kubernetes runs. Kubernetes never runs a
  container directly; it always wraps it in a pod.
- Usually 1 pod = 1 container (my app).
- A pod can hold more than one container when they must live together, e.g.
  the app plus a helper container. They share the same network (localhost)
  and can share storage.
- To scale, Kubernetes adds **more pods**, not more containers inside one pod.

### kubectl basics
- `kubectl run` creates a pod quickly from the command line.
- `kubectl get pods` lists pods; `-o wide` also shows the IP and node.
- `kubectl describe pod <name>` shows details and **Events** at the bottom,
  the first place to look when something fails.

### YAML
- YAML is a human-readable format for configuration. Kubernetes manifests are
  written in YAML.
- **Key-value pair:** `name: nginx`
- **Dictionary (map):** a group of key-value pairs under one key, indented
  the same amount. Order does not matter.
- **List:** items that start with `-`. Order matters.
- **Indentation = structure.** Use spaces only (never tabs), same number of
  spaces for items at the same level.


## Self-check questions

**Q: What's the difference between a YAML list and a dictionary?**
A: A dictionary is a set of key-value pairs where order doesn't matter (like
the properties of one object). A list is an ordered collection of items, each
starting with `-` (like several containers in one pod). `containers` is a list
because a pod can have more than one container.

## Commands I used
```bash
kubectl run nginx --image=nginx        # quick pod from the command line
kubectl get pods                       # list pods
kubectl get pods -o wide               # with IP and node


## Next
Pods and ReplicaSets.