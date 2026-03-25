# Krash Kourse v0 - My first pod

In this exercise, we'll just deploy a single Kubernetes pod.

## 1 Create the pod

Kubernetes uses yaml files to define objects declaratively.
You can also create objects with imperative commands like `kubectl create pod`, but this is less maintainable.

```bash
kubectl apply -f configs/v0/lt-pod.yaml
```

> Expected output:
>
> ```plain
> pod/lt-pod created
> ```

## 2 Inspect the pod

Inspect the pod with the following command.
It might take a few seconds until it's ready.

```bash
kubectl get pods
```

> Expected output:
>
> ```plain
> NAME     READY   STATUS    RESTARTS   AGE
> lt-pod   1/1     Running   0          43s
> ```

While you're waiting, let's try some CLI args to add to the above command:

* `-o wide`: More columns.
* `-A`: Shows pods in all namespaces.
* `-o yaml`: Outputs YAML.
* `-n kube-system`: Only shows pods in the `kube-system` namespace.
* `kubectl get pods -l app=languagetool`: Only shows pods that have the label `app` set to `languagetool`.

## 3 Access the deployed pod

To send an HTTP request to the LanguageTool pod we just created, we need to be _inside_ the Kubernetes node.
In `k3s`, that would just mean being on the same machine, but Docker Desktop actually uses a lightweight VM, so we have to take a workaround.
We use `kubectl run` to spawn another pod that does nothing but run a single curl command and then exit.

On Windows, it's recommended to use the WSL to run the below command, but you can also go step by step to first get the pod IP using the `kubectl get pods ...` command and then just piece together the required `kubectl run ...` command by manual copy and paste.

```bash
# Find the IP of the pod that runs LT.
LT_POD_IP=$(kubectl get pods -l app=languagetool -o=jsonpath='{.items[0].status.podIP}')
# Send curl command to get supported languages. Expected output: [{"name":"Arabic", ...}]
kubectl run -itq --restart=Never --rm --image curlimages/curl curl -- $LT_POD_IP:8010/v2/languages
# Send curl command to correct a tiny sentence.
kubectl run -itq --restart=Never --rm --image curlimages/curl curl -- $LT_POD_IP:8010/v2/check -d text="Its fine." -d language=en-US
```

> Expected output (abbreviated):
>
> ```plain
> [{"name":"Arabic", ... }]
> {"software":{"name":"LanguageTool", ... }
> ```

## 4 Cleanup

Delete the pod to be ready for the next exercise:

```bash
kubectl delete pod lt-pod
```
