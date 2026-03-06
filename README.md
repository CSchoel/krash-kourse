# Krash kourse

This is a repository for getting your hands dirty with Kubernetes in less than an hour.

## Initial setup

For the sake of simplicity, we're using a single-node Kubernetes cluster that you can just set up on your development machine.

* _Note: If you have a firewall like `ufd` or `firewalld`, you might want to disable it for this course or allow the following connections:_
  * TCP on port `6443` for the `kube-apiserver`.
  * Any connection from `10.42.0.0/16` to any other IP for the Kubernetes pods.
  * Any connection from `10.43.0.0/16` to any other IP for the Kubernetes services.
* Install k3s on your machine: `curl -sfL https://get.k3s.io | sh -`.
  * You might need to enable some kernel settings, but k3s will tell you about that.
* Check that everything works with `kubectl get nodes`. It should show something like this:
    ```plain
    NAME      STATUS   ROLES           AGE   VERSION
    valakas   Ready    control-plane   57d   v1.34.3+k3s1
    ```
