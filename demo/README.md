# Demo

* Sock Shop
> http://<cluster ip>:30001

```
$ kubectl create ns sock-shop
$ kubectl apply -f demo/yaml/sock-shop.yaml
```

* BookInfo

```
$ kubectl create ns bookinfo 
$ kubectl apply -n bookinfo -f https://raw.githubusercontent.com/istio/istio/master/samples/bookinfo/platform/kube/bookinfo.yaml
```

* Weave Scope

```
$ kubectl apply -f "https://cloud.weave.works/k8s/scope.yaml?k8s-version=$(kubectl version | base64 | tr -d '\n')"
$ kubectl port-forward -n weave service/weave-scope-app 4040:80
```