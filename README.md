# Krash Kourse

This is a repository for getting your hands dirty with Kubernetes in less than an hour.

## Requirements

For this tutorial, you need the following things:

* A machine where you can install [Docker Desktop](https://www.docker.com/products/docker-desktop/).
  * This should work on Linux, macOS, and Windows.
  * You can find installation instructions in the next section.
  * If you're on Linux, you can also use [k3s](https://docs.k3s.io/) instead.
* [Git](https://git-scm.com/).
* The `curl` command.
  * Linux: Might be alread there. If not, just install the appropriate package for your favorite package manager.
  * macOS: You already have curl.
  * Windows: You already have curl, but in PowerShell there is a weird alias that is _called_ curl but uses an entriely different function, so you have to type `curl.exe` or use curl on the WSL instead.
* Optional: There are a few instances where I use `sed` on the terminal. Instead of using that, you can also just use an editor of your choice and change the file according to the comment above the command.

## Initial setup

For the sake of simplicity, we're using a single-node Kubernetes cluster that you can just set up on your development machine.
We assume the use of [Docker Desktop](https://www.docker.com/products/docker-desktop/) in this tutorial since it works for Linux, Windows, and macOS.

* Download and install [Docker Desktop](https://www.docker.com/products/docker-desktop/) for your OS.
* [Enable Kubernetes in Docker Desktop](https://docs.docker.com/desktop/use-desktop/kubernetes/#enable-kubernetes) with the following choices:
  * Use `kubeadm`.
  * Select "Show system containers (advanced)". (We don't really need this, but it allows you to inspect the system in more detail.)
* Check that everything works with `kubectl get nodes`. It should show something like this:
    ```plain
    NAME                 STATUS    ROLES            AGE       VERSION
    docker-desktop       Ready     control-plane    3h        v1.29.1
    ```

## Exercises

To complete your Krash Kourse, work your way through the exercises in the `configs` folder, starting with [v0](configs/v0/README.md).
