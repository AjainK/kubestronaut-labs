# 03 · Pods and ReplicaSets
Date: 2026-11-04 · Course: KodeKloud – Kubernetes for the Absolute Beginners

## What I learned

### Pods with YAML
- I can create any object from a YAML file with `kubectl apply -f <file>`.
- `kubectl describe pod` shows the image, node, IP and **Events**, the first
  place to look when a pod won't start.

### Why ReplicaSets exist
- A single pod has no safety net: if it crashes or its node dies, it's gone.
- A **ReplicaSet** keeps a fixed number of identical pods running at all
  times. If one dies, it creates a replacement automatically (self-healing).
- It also lets me run several copies of my app to share load.

### ReplicationController vs ReplicaSet
- ReplicationController is the older version (`apiVersion: v1`).
- ReplicaSet is the newer one (`apiVersion: apps/v1`) and needs a `selector`.
- In practice I'll rarely create either directly; Deployments create
  ReplicaSets for me (next session).

### How a ReplicaSet finds its pods
- It doesn't track pods by name. It uses **labels**.
- `spec.selector.matchLabels` must match the labels in `spec.template.metadata.labels`.
- The `template` is the pod blueprint it uses to create new pods.

### Scaling
- Change `replicas` in the YAML and apply it again, or use
  `kubectl scale rs <name> --replicas=6`.

## Self-check questions

**Q: Why does a ReplicaSet need a selector?**
A: The selector is how the ReplicaSet decides which pods belong to it. It
counts every pod whose labels match, and adds or removes pods to keep that
count equal to `replicas`. It can even take over matching pods that existed
before it was created.

**Q: What happens if you manually create a pod with the same labels?**
A: The ReplicaSet counts it as one of its own. If that makes the total higher
than `replicas`, the ReplicaSet deletes a pod to get back to the desired
number, so my manual pod may be removed straight away.

## Commands I used
```bash
kubectl apply -f replicaset.yaml
kubectl get replicaset                  # or: kubectl get rs
kubectl get pods --show-labels
kubectl describe rs myapp-rs
kubectl delete pod <one-of-the-pods>    # watch a replacement appear
kubectl get pods -w                     # watch live (Ctrl+C to stop)
kubectl scale rs myapp-rs --replicas=5
kubectl delete rs myapp-rs              # also deletes its pods
```

## What confused me / mistakes I made
- my ReplicaSet failed with "selector does not match template
  labels". The labels under selector and template must be identical.


## Next
Deployments, rolling updates and rollbacks.