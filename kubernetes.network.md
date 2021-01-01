# Kubernetes Network

![Kubernetes 네트워크](https://miro.medium.com/max/1400/1*qq0ioT5k9eazkHH402h3mw.png)
* [출처 - Kubernetes 네트워크 정리](https://sookocheff.com/post/kubernetes/understanding-kubernetes-networking-model/)

## DNS

* <service>.<namespace>.<svc>
* <pod>.<namespace>.<pod>

```
▒ kubectl apply -f https://k8s.io/examples/admin/dns/dnsutils.yaml
▒ kubectl exec -it -n default dnsutils -- nslookup kubernetes.default
```