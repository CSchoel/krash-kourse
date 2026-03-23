# Krash Kourse v3 - Rolling out new versions

Now that we have a nice service in place, let's think about what happens if we want to upgrade to a new version of the LT docker container:

```bash
# Check image version before update
kubectl get pods -l app=languagetool -o=jsonpath="{.items[*].spec.containers[0].image}{'\n'}"
# Update ReplicaSet
kubectl apply -f configs/v3/lt-replicaset.yaml
# Check image version after update
kubectl get pods -l app=languagetool -o=jsonpath="{.items[*].spec.containers[0].image}{'\n'}"
```

Expected output:

```plain
erikvl87/languagetool:6.7-dockerupdate-3 erikvl87/languagetool:6.7-dockerupdate-3 erikvl87/languagetool:6.7-dockerupdate-3
replicaset.apps/lt-replicaset configured
erikvl87/languagetool:6.7-dockerupdate-3 erikvl87/languagetool:6.7-dockerupdate-3 erikvl87/languagetool:6.7-dockerupdate-3
```

In fact, the image version of the pods remains unaffected by the change to the `ReplicaSet`.
To make the changes take effect, we have to delete the pods first, using `kubectl delete pod name-of-pod`.

Thats both tedious and depending on how fast we delete pods and how fast the new ones start, the service could be down for some time.
The solution is a `Deployment`, which watches image versions and other changes and automatically deletes and re-creates pods according to a rollout strategy.

## 1 Create a Deployment

```bash
# Delete old ReplicaSet
kubectl delete -f configs/v3/lt-replicaset.yaml
# Create new Deployment
kubectl apply -f configs/v3/lt-deployment.yaml
```

Expected output:

```plain
replicaset.apps "lt-replicaset" deleted from default namespace
deployment.apps/lt-deployment created
```

## 2 Change the version and watch pod changes

In this section, we use `watch` for changes.
Windows users and macOS users without `watch` installed can just omit the part starting with `&&` and repeatedly call `kubectl get pods ...` manually.

```bash
# Replace "6.7-dockerupdate-3" with "6.6-dockerupdate-2" in lt-deployment.yaml.
sed -i 's/6.7-dockerupdate-3/6.6-dockerupdate-2/' configs/v3/lt-deployment.yaml
# Apply and watch changes
kubectl apply -f configs/v3/lt-deployment.yaml && watch -n 1 kubectl get pods -l app=languagetool
```

Expected output:

* You should see some pods being deleted and new ones being created.
* There should always be at least one pod that remains running.

## 3 Check the rollout history

```bash
kubectl rollout history deployment/lt-deployment 
```

Expected output:

```plain
deployment.apps/lt-deployment
REVISION  CHANGE-CAUSE
1         <none>
2         <none>
```

Note: The `CHANGE-CAUSE` can be set as an annotation in the deployment yaml file or (if you use imperative commands instead of `apply`) via an argument to `kubectl`.

## 4 Roll back to the previous version

```bash
kubectl rollout undo deployment/lt-deployment && watch -n 1 kubectl get pods -l app=languagetool
```

Expected output:

* You should see some pods being deleted and new ones being created.
* There should always be at least one pod that remains running.
