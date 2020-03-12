# Kubernetes 관련 메모
> 깜빡깜빡하는 늙은 개발자를 위한 정리

## 문서

* [docker.md](./docker.md)
* [kubectl.md](./kubectl.md)
* [helm.md](./helm.md)
* [minikube.md](./minikube.md)
* [shell.md](./shell.md)

## Helm Chart

* [httpbin](./charts/httpbin)

## Istio

* [install istio](./istio/install.md)
* [install kiali](./istio/install.kiali.md)
* [cli](./istio/cli.md)


## 링크

* [kubectl 치트 시트](https://kubernetes.io/ko/docs/reference/kubectl/cheatsheet/)
* [kubernetes api](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.17/)

## 기타

* BusyBox
~~~
apiVersion: v1
kind: Pod
metadata:
  name: busybox
  namespace: default
spec:
  containers:
  - name: busybox
    image: busybox:1.28
    command:
      - sleep
      - "3600"
    imagePullPolicy: IfNotPresent
  restartPolicy: Always
~~~

~~~
▒ kubectl exec -n default -ti busybox -- nslookup kubernetes.default
~~~

* httpbin
~~~
apiVersion: v1
kind: Pod
metadata:
  name: httpbin
  labels:
    app: httpbin
spec:
  containers:
  - image: docker.io/honester/httpbin:latest
    imagePullPolicy: IfNotPresent
    name: httpbin
~~~

~~~
▒ do kubectl exec -it httpbin -c httpbin -- curl http://svc-hello.default:8080;
▒ for i in {1..20}; do kubectl exec -it httpbin -c httpbin -- curl http://svc-hello.default:8080; sleep 0.1; done
~~~
