# 04 · Deployments, updates and rollbacks
Date: 2026-11-05 · Course: KodeKloud – Kubernetes for the Absolute Beginners

## What I learned

### Pod → ReplicaSet → Deployment
- **Pod:** runs my container.
- **ReplicaSet:** keeps N copies of the pod running.
- **Deployment:** manages ReplicaSets, and adds safe **updates** and
  **rollbacks** on top.
- When I create a Deployment, it creates a ReplicaSet, which creates the pods.
  (`kubectl get all` shows all three.)

### Rolling updates
- When I change the image (e.g. nginx 1.24 → 1.25), the Deployment creates a
  **new ReplicaSet** and gradually moves pods from the old one to the new one.
- The app stays available during the update: old pods are only removed once
  new ones are running.
- The old ReplicaSet is kept (with 0 pods), which is what makes rollback possible.

### Two update strategies
| Strategy | What happens | Downtime? |
|---|---|---|
| **RollingUpdate** (default) | Replaces pods a few at a time | No |
| **Recreate** | Deletes all old pods, then creates new ones | Yes, briefly |

### Rollback
- `kubectl rollout undo` switches back to the previous ReplicaSet.
- Each change creates a new **revision**; `kubectl rollout history` lists them.

## Self-check questions

**Q: Recreate vs RollingUpdate: when would you use each?**
A: RollingUpdate for almost everything, because users see no downtime.
Recreate only when the old and new versions must never run at the same time,
e.g. a breaking database schema change, or an app that can't run two copies.

**Q: Where does Kubernetes keep the rollout history?**
A: In the old ReplicaSets. Each revision is a ReplicaSet the Deployment keeps
(scaled to 0). By default it keeps the last 10 (`revisionHistoryLimit`).
Rolling back simply scales an old ReplicaSet up again.

## Commands I used
```bash
kubectl apply -f deployment.yaml
kubectl get deployments
kubectl get all                                         # deployment, rs and pods
kubectl set image deployment/myapp-deploy nginx=nginx:1.25
kubectl rollout status deployment/myapp-deploy          # watch the update
kubectl rollout history deployment/myapp-deploy         # list revisions
kubectl rollout undo deployment/myapp-deploy            # go back one revision
kubectl get rs                                          # see old and new ReplicaSets
```

## What confused me / mistakes I made
- After the update I saw two ReplicaSets and thought something was
  wrong. The old one is kept at 0 pods so I can roll back.

## Next
Networking and Services.