# Krash Kourse v0 - My first pod

In this exercise, we'll just deploy a single Kubernetes pod.

## 1 Create the pod

Kubernetes uses yaml files to define objects declaratively.
You can also create objects with imperative commands like `kubectl create pod`, but this is less maintainable.

```bash
kubectl apply -f configs/v0/lt-pod.yaml
```

## 2 Inspect the pod

Inspect the pod with the following command.
It might take a few seconds until it's ready.

```bash
kubectl get pods
```

While you're waiting, let's try some CLI args to add to the above command:

* `-o wide`: More columns.
* `-A`: Shows pods in all namespaces.
* `-o yaml`: Outputs YAML.
* `-n kube-system`: Only shows pods in the `kube-system` namespace.
* `kubectl get pods -l app=languagetool`: Only shows pods that have the label `app` set to `languagetool`.

## 3 Access the deployed service

```bash
LT_POD_IP=$(kubectl get pods -l app=languagetool -o=jsonpath='{.items[0].status.podIP}')
curl $LT_POD_IP:8010/v2/languages
curl $LT_POD_IP:8010/v2/check -d text="Its fine." -d language=en-US
```

## 4 Cleanup

Delete the pod to be ready for the next exercise:

```bash
kubectl delete pod lt-pod
```
