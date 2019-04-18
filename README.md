# k8s-lib-demo

Deploy nginx ingress with [ytt](https://github.com/k14s/ytt) and [kapp](https://github.com/k14s/kapp)

```bash
$ ytt t -R -f nginx-ingress/ | kapp -y deploy -a nginx-ingress -f -
```

(Included nginx ingress is not configured for production use, only for demo purposes)

## apps

Deploy two apps (see `apps/hello1.yml` for definition; relies on [k8s-lib](https://github.com/k14s/k8s-lib))

```bash
$ ytt t -R -f apps/ | kapp -y deploy -a apps -f -
```

See that there is only one hello1 Pod

```bash
$ kapp inspect -a apps -t
```

Expose ingress to your machine with [kwt](https://github.com/k14s/kwt)

```bash
$ sudo -E kwt net start
```

Check that app successfully responds with configured text

```bash
$ curl http://nginx-ingress-controller.default.svc.cluster.local/
```

Throw some load at the app, and you should see it be autoscaled some time after

```bash
$ siege -c 100 http://nginx-ingress-controller.default.svc.cluster.local/
```

(In my setup, it took several minutes for HPA to scale up/down)

You can also access second app

```bash
$ curl http://nginx-ingress-controller.default.svc.cluster.local/ -H "Host: hello2.com"
```

## simple-app (to exercise kbld)

Deploy simple-app with help of [ytt](https://github.com/k14s/ytt), [kbld](https://github.com/k14s/kbld), and [kapp](https://github.com/k14s/kapp)

```bash
$ ytt t -R -f . | kbld apply -f - | kapp -y deploy -a simple-app -f - --diff-changes
```

`kbld` requires Docker CLI (`docker`) available on $PATH as it builds a container based on `simple-app/src` directory. You can grab Docker CLI binaries [here](https://docs.docker.com/install/linux/docker-ce/binaries/). If you are using minikube you'll have to expose Docker daemon via `eval $(minikube docker-env)`. Otherwise you will have to add configuration (below) for pushing images to `simple-app/manifest.yml` so that built image is available to your Kubernetes cluster.

```yaml
---
apiVersion: kbld.k14s.io/v1alpha1
kind: ImageDestinations
destinations:
- image: simple-app-image
  newImage: docker.io/dkalinin/simple-app # or whatever push target your Docker can push to
```
