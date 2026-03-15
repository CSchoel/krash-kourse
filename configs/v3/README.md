# Krash Kourse v3 - Rolling out new versions

Now that we have a nice service in place, let's think about what happens if we want to upgrade to a new version of the LT docker container:

```bash
# Check image version vefore update
kubectl get pods -l app=languagetool -o yaml | grep "image: docker.io/"
# Update ReplicaSet
kubectl apply -f configs/v3/lt-replicaset.yaml
# Check image version after update
k get pods -l app=languagetool -o yaml | grep "image: docker.io/"
```

In fact, the pods remain unaffected by the change to the `ReplicaSet`.
To make the changes take effect, we have to delete the pods first, using `kubectl delete pod name-of-pod`.

Thats both tedious and depending on how fast we delete pods and how fast the new ones start, the service could be down for some time.
The solution is a `Deployment`, which watches image versions and other changes and automatically deletes and re-creates pods according to a rollout strategy.

## 1 Create a Deployment

```bash
kubectl delete -f configs/v3/lt-replicaset.yaml # delete ReplicaSet if needed
kubectl apply -f configs/v3/lt-deployment.yaml
```

## 2 Change the version and watch pod changes

```bash
sed -i 's/6.7-dockerupdate-3/6.6-dockerupdate-2/' config/v3/lt-deployment.yaml

```
