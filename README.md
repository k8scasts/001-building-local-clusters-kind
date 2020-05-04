# Building Local Clusters with kind

## Table of Contents

- [Table of Contents](#table-of-contents)
- [Background](#background)
- [Show Notes](#show-notes)
- [Links](#links)
- [Mentions](#mentions)

## Background

Do you want to learn about Kubernetes? The best way is to just start playing and experimenting. To do this we need a local cluster to play with, _it's hard to get a cheaper cluster than running locally anymore_, to do this we can use a number of tools, today I'm going to focus on my favorite [`kind`](https://kind.sigs.k8s.io). kind deploying Kubernetes into Docker containers running on your machine as though they were physical machines. Watch the video to learn more.

## Show Notes

Using kind requires that you have a machine available that can run `docker`, depending on you operating system you can install this by following the [Docker Installation Guide](https://docs.docker.com/engine/install/#supported-platforms).

Once you have `docker` installed you will need to make sure you also have [`go (1.11+)`](https://golang.org/) installed. 

After that all you need to do is install `kind` using `go`.

```bash
GO111MODULE="on" go get sigs.k8s.io/kind@v0.8.0
```
<!-- https://gist.github.com/christopherhein/de81b5ff1a332652f948a82abdee5920 -->


With `kind` installed and `$GOBIN` in your path you should be able to create your first Kubernetes cluster using `kind`.

```bash
kind create cluster
```
<!-- https://gist.github.com/christopherhein/aae3e3e8a24cb4c136d20c68499fb7e2-->

This cluster is incredibly lightweight; only a single `docker` container both running the control plane and the data plane with in that "node". We can see this by running. 

```bash
docker images
```
<!-- https://gist.github.com/christopherhein/14501668172edd70b26c1f039ae1f23d -->

We can explore this cluster using `kubectl` which will use the local `KUBECONFIG` path or by default `~/.kube/config`.

```bash
kubectl cluster info
```
<!-- https://gist.github.com/christopherhein/eaa6dcf171a4f4559df31c178d843945 -->

Now that you have a cluster you can try deploying something on to it.

```bash
kubectl run --image nginx:latest nginx
```
<!-- https://gist.github.com/christopherhein/d07ffbac40edfd0e8bf8852b4e0b8605 -->

We can see this running by inspecting pods with `kubectl`.

```bash
kubectl get pods
```
<!-- https://gist.github.com/christopherhein/c490d40e09e313b4f8117331af8941aa -->

You can even run multiple `kind` clusters at once on your machine by passing in other names.

```bash
kind create cluster --name test-cluster
```
<!-- https://gist.github.com/christopherhein/15dfc4fb96bb6fada77544309ff417ef -->

This allows you to work on multiple projects at the same time. First we can see our both our clusters.

```bash
kind get clusters
```
<!-- https://gist.github.com/christopherhein/458c824ce95a2f6bdf52029ba01b3ca6 -->

We can switch back and forth between these using `kubectl` and `--context` switching.

```bash
kubectl cluster-info --context kind-kind
kubectl cluster-info --context kind-test-cluster
```
<!-- https://gist.github.com/christopherhein/26c9c48cbc3e67147f9651274e7fec13 -->

Sometimes you want to see what it's like when your application is deployed across multiple nodes, this can be useful to simulate how your application behaves under failure modes for example. `kind` make this very easy by allowing you to submit a config file. This config file contains a lot of options and I will have a link to the godocs for it in the links below.

```yaml
# kind-config.yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
- role: worker
- role: worker
```
<!-- https://gist.github.com/christopherhein/8f9d6628b60a1e38f9fe769ebc5d8274 -->

```bash
kind cluster create --config kind-config.yaml
```
<!-- https://gist.github.com/christopherhein/4348831e57b0f64a6c70f767f16c5a52 -->

Now if you list `docker images` you will see multiple nodes each a separate docker image.

```bash
docker images
```
<!-- https://gist.github.com/christopherhein/14501668172edd70b26c1f039ae1f23d -->

To clean up your clusters you can run `kind delete cluster` and passing `--name` to specify the `test-cluster`.

```bash
kind delete cluster
```
<!-- https://gist.github.com/christopherhein/eb11f8b83eb1e2ce8b780c0f36e30941 -->

## Links

- [https://golang.org/doc/install](https://golang.org/doc/install)
- [https://kind.sigs.k8s.io/](https://kind.sigs.k8s.io/)
- [https://github.com/kubernetes-sigs/kind/](https://github.com/kubernetes-sigs/kind/)
- [https://pkg.go.dev/sigs.k8s.io/kind/pkg/apis/config/v1alpha4?tab=doc](https://pkg.go.dev/sigs.k8s.io/kind/pkg/apis/config/v1alpha4?tab=doc)

## Mentions

- Ben Elder
  - [https://elder.dev/](https://elder.dev/)
  - [https://github.com/BenTheElder](https://github.com/BenTheElder)
  - [https://twitter.com/BenTheElder](https://twitter.com/BenTheElder)