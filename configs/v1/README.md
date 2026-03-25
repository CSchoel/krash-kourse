# Krash Kourse v1 - Scaling the number of pods with a ReplicaSet

Kubernetes is all about automation and scalability, so instead of deploying a pod directly, we now deploy a `ReplicaSet` that allows us to dynamically adjust the number of pods as needed.

## 1 Deploy the replicaset

```bash
kubectl apply -f configs/v1/lt-replicaset.yaml
```

> Expected output:
>
> ```plain
> replicaset.apps/lt-replicaset created
> ```

## 2 Observe pods

```bash
kubectl get pods
```

> Expected output:
>
> ```plain
> NAME                  READY   STATUS    RESTARTS   AGE
> lt-replicaset-nl4j4   1/1     Running   0          36s
> lt-replicaset-qgb6l   1/1     Running   0          36s
> lt-replicaset-qhs95   1/1     Running   0          36s
> ```

## 3 Scale number of pods (imperative)

```bash
kubectl scale replicaset lt-replicaset --replicas=2
```

> Expected output:
>
> ```plain
> replicaset.apps/lt-replicaset scaled
> ```

## 4 Scale number of pods (declarative)

```bash
# Change "replicas: X" to "replicas: 4" in lt-replicaset.yaml
sed -Ei 's/replicas: [0-9]+/replicas: 4/' configs/v1/lt-replicaset.yaml
# Apply update
kubectl apply -f configs/v1/lt-replicaset.yaml
```

> Expected output:
>
> ```plain
> replicaset.apps/lt-replicaset configured
> ```
