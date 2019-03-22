# k8s-lib-demo

Deploy nginx ingress

```bash
$ ytt t -R -f nginx-ingress | kapp -y deploy -a nginx-ingress -f -
```

Deploy app:

```bash
$ ytt t -R -f app | kapp -y deploy -a app1 -f -
```

See that there is only one Pod that's running the app

```bash
$ kapp inspect -a app1 -t
```

Expose ingress to your machine

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
