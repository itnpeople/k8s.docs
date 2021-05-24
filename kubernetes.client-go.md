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

### 로컬에서 in-cluster 개발 테스트
* token 파일(`/var/run/secrets/kubernetes.io/serviceaccount/token`)
* certificate 파일 (`/var/run/secrets/kubernetes.io/serviceaccount/ca.crt`)
* api-server 환경변수 `KUBERNETES_SERVICE_HOST`, `KUBERNETES_SERVICE_PORT`


* 특정 service account 에서 TOKEN, CA 조회

```
▒ NAMESPACE="kore"
▒ SA="kore-board"
▒ SECRET="$(kubectl get sa ${SA} -n kore -o jsonpath="{.secrets..name}")"

▒ TOKEN="$(kubectl get secrets ${SECRET} -n ${NAMESPACE} -o jsonpath="{.data.token}"|base64 --decode)"
▒ CA="$(kubectl get secrets  ${SECRET} -n ${NAMESPACE} -o jsonpath="{.data.ca\.crt}"|base64 --decode)"
```
cvl
* token, ca.crt, 파일 지정 , KUBERNETES_SERVICE_HOST, KUBERNETES_SERVICE_PORT 환경변수 지정

```
▒ CLUSTER="?(@.name==\"minikube\")"    # or "0", "1" ....
▒ sudo mkdir -p /var/run/secrets/kubernetes.io/serviceaccount/

▒ echo -n "${TOKEN}" | sudo tee /var/run/secrets/kubernetes.io/serviceaccount/token
▒ echo -n "${CA}" | sudo tee /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
▒ export KUBERNETES_SERVICE_HOST=$(kubectl config view --raw=true -o jsonpath="{.clusters[${CLUSTER}].cluster.server}" |  awk -F/ '{print $3}' |  awk -F: '{print $1}')
▒ export KUBERNETES_SERVICE_PORT=$(kubectl config view --raw=true -o jsonpath="{.clusters[${CLUSTER}].cluster.server}" |  awk -F/ '{print $3}' |  awk -F: '{print $2}')
```

* default service account 에서 TOKEN, CA 조회하는 경우

```
▒ TOKEN=$(kubectl get secrets -o jsonpath="{.items[?(@.metadata.annotations['kubernetes\.io/service-account\.name']=='default')].data.token}"|base64 --decode)
▒ CA=$(kubectl get secrets -o jsonpath="{.items[?(@.metadata.annotations['kubernetes\.io/service-account\.name']=='default')].data.ca\.crt}"|base64 --decode)
```


### 필요시 권한 부여
> User "system:serviceaccount:default:default" cannot get resource "configmaps" in API group "" in the namespace "default"

* 예 - default namespace 의 default serviceaccount 에 `cluster-admin` cluster-role 를 지정

```
$ kubeclt apply -f - <<EOF
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding 
metadata:
  name: crb-default
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: default
    namespace: default
EOF
```


## Kubernetes 의 Downward API
> 컨테이너에서 실행 중인 애플리케이션의 속성을 환경변수로 전달

* https://sgc109.github.io/2021/01/17/k8s-downward-api/

