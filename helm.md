# Helm

* https://github.com/helm/charts


## Repository

* repository 추가

```
▒ helm repo add stable https://kubernetes-charts.storage.googleapis.com/
▒ helm repo add incubator https://kubernetes-charts-incubator.storage.googleapis.com/
▒ helm repo update
```

* repoistory에서 chart 검색

```
▒ helm search repo nginx

stable/nginx-ingress            1.33.5          0.30.0          An nginx Ingress controller that uses ConfigMap...
stable/nginx-ldapauth-proxy     0.1.3           1.13.5          nginx proxy with ldapauth                         
stable/nginx-lego               0.3.1                           Chart for nginx-ingress-controller and kube-lego  
stable/gcloud-endpoints         0.1.2           1               DEPRECATED Develop, deploy, protect and monitor...
```

* Web : https://hub.helm.sh/charts
