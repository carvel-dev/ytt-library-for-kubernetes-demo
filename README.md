# k8s-lib-demo

Deploy nginx ingress with [ytt](https://github.com/k14s/ytt) and [kapp](https://github.com/k14s/kapp)

```bash
$ ytt t -R -f nginx-ingress/ | kapp -y deploy -a nginx-ingress -f -
```

(Included nginx ingress is not configured for production use, only for demo purposes)

Deploy app (see `app1/app.yml` for definition; relies on [k8s-lib](https://github.com/k14s/k8s-lib))

```bash
$ ytt t -R -f app1/ | kapp -y deploy -a app1 -f -
```

See that there is only one Pod that's running the app

```bash
$ kapp inspect -a app1 -t
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
