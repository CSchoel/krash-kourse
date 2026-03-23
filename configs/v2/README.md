# Krash Kourse v2 - Using a service

In [v0](../v0/README.md), we were able to use `curl` to query the LT server using the IP of the _Kubernetes pod_ that runs the service.
With the `ReplicaSet` introduced in [v1](../v1/README.md), this already becomes unwieldy.
_Which_ pod should we query?
Would we just skip between multiple IPs, or do we have to deploy a load balancer?

If we think about a production setup, we even have another more fundamental problem: All Kubernetes componens, including our LT pods, live in a private network.
The example in v0 only worked, because we happened to be on the same machine that hosts this network.
So how do we let users access the LT pod from outside the cluster?
Or in other words, how do we make something like the following work:

```bash
# This won't work yet!
curl 127.0.0.1:8010/v2/languages
```

The answer is a `Service`, specifically a `NodePort` service that acts as a per-node load-balancer for our LT pods.
In a multi-node setup, you would want a `LoadBalancer` service instead (which only works in a managed Kubernetes cloud) or actually deploy a load-balancer like traefik yourself.

## 1 Deploy a NodePort service

```bash
kubectl apply -f configs/v2/lt-service.yaml
```

Expected output:

```plain
service/lt-service created
```

## 2 Test access from outside the cluster

```bash
curl 127.0.0.1:30080/v2/languages
```

Expected output:

```plain
[{"name":"Arabic", ... }]
```

## 3 Check pod logs

```bash
curl 127.0.0.1:30080/v2/languages
curl 127.0.0.1:30080/v2/languages
curl 127.0.0.1:30080/v2/languages
kubectl logs -l app=languagetool --tail 5 --all-pods=true | grep Handling
```

Expected output:

```plain
[pod/lt-replicaset-nl4j4/lt] 2026-03-23 16:36:30.960 GMT INFO  org.languagetool.server.LanguageToolHttpHandler Handling GET /v2/languages
[pod/lt-replicaset-qhs95/lt] 2026-03-23 16:36:29.244 GMT INFO  org.languagetool.server.LanguageToolHttpHandler Handling GET /v2/languages
[pod/lt-replicaset-qhs95/lt] 2026-03-23 16:36:29.961 GMT INFO  org.languagetool.server.LanguageToolHttpHandler Handling GET /v2/languages
[pod/lt-replicaset-xph5s/lt] 2026-03-23 16:36:30.473 GMT INFO  org.languagetool.server.LanguageToolHttpHandler Handling GET /v2/languages
```

_Note: You should see that the requests are distributed evenly across the pods._
