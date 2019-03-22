# k8s-lib-demo

Deploy nginx ingress with [ytt](https://github.com/k14s/ytt) and [kapp](https://github.com/k14s/kapp)

```bash
$ ytt t -R -f nginx-ingress/ | kapp -y deploy -a nginx-ingress -f -
```

(Included nginx ingress is not configured for production use, only for demo purposes)

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
