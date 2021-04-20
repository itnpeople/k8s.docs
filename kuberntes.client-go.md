# Kubernetes client-go

## kubernetes api
* [v1.17](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.17/)


## client-go In-cluster 테스트 

```
import (
  "k8s.io/client-go/rest"
)
...

rest.InClusterConfig()
```

* 아래처럼 환경변수 2개 + token 파일 + certificate 파일 지정하면 로컬에서 in-cluster 개발 테스트 가능

```
sudo mkdir -p /var/run/secrets/kubernetes.io/serviceaccount/
echo "$(kubectl get secret -n acornsoft-dashboard $(kubectl get sa acornsoft-dashboard -n acornsoft-dashboard -o jsonpath={.secrets..name} | cut -f1 -d ' ') -o jsonpath='{$.data.token}' | base64 --decode)" | sudo tee /var/run/secrets/kubernetes.io/serviceaccount/token
echo "$(kubectl config view --raw=true -o jsonpath='{.clusters[0].cluster.certificate-authority-data}' | base64 --decode)" | sudo tee /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
export KUBERNETES_SERVICE_HOST=$(kubectl config view --raw=true -o jsonpath='{.clusters[0].cluster.server}' |  awk -F/ '{print $3}' |  awk -F: '{print $1}')
export KUBERNETES_SERVICE_PORT=$(kubectl config view --raw=true -o jsonpath='{.clusters[0].cluster.server}' |  awk -F/ '{print $3}' |  awk -F: '{print $2}')
```