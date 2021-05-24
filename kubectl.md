# kubectl 활용
> [kubectl 치트 시트](https://kubernetes.io/ko/docs/reference/kubectl/cheatsheet/)

## Get (JSONPATH)

* 레이블
~~~
▒ kubectl get pod -l app=hello
▒ kubectl get pod -l app.kubernetes.io/name=backend
~~~

* Array

~~~
▒ kubectl get pod busybox -o jsonpath={.items..metadata.name}
~~~

* array 필터링 `[?(@.name=="http")]`

~~~
▒ kubectl get svc/frontend -o jsonpath='{.spec.ports[?(@.name=="http")].nodePort}'
~~~


## Delete

* 네임스페이스 리소스 일괄삭제 (`-o name` 응용)

```
▒ kubectl get all -n default -o name |xargs kubectl delete -n default
```

* CRD 일괄 삭제 명령어

```
▒ kubectl get crd |grep management.cattle.io | awk '{print $1}' |xargs kubectl delete crd
```

* 해당 CRD 리소스 일곽 삭제 명령어
```
▒ kubectl  get "users.management.cattle.io" | awk 'NR > 1 {print $1}' |xargs kubectl delete "users.management.cattle.io"
```

## Logs

~~~
▒ kubectl logs $(kubectl get pod -l app=hello -o jsonpath={.items..metadata.name}) -c hello-server-v1"
~~~

## Run

* busybox
~~~
▒ kubectl run -it --rm --restart=Never busybox --image=busybox sh
~~~


## Port Forward

* service 포트 포워딩
~~~
▒ kubectl port-forward svc/productpage -n default 9080                                         # 일반
▒ kubectl port-forward --context acorn-rnd svc/productpage -n default 9080                     # Context 지정
▒ kubectl port-forward --context acorn-rnd svc/productpage -n default 9080 > /dev/null         # 출력 없음
▒ kubectl port-forward --context acorn-rnd svc/productpage -n default 9080 2> /dev/null        # 출력 & 에러 없음
▒ kubectl port-forward --context acorn-rnd svc/productpage -n default 9080 > /dev/null 2>&1 &  # 출력 & 에러 + 데몬
~~~

* Local 8080 포트로 포워딩 
~~~
▒ kubectl port-forward httpbin 8080:80
~~~

* 포워딩 예제
~~~
▒ kubectl -n istio-system port-forward $(kubectl -n istio-system get pod -l app=prometheus -o jsonpath='{.items[0].metadata.name}') 9090:9090   # Prometheus
▒ kubectl -n istio-system port-forward $(kubectl -n istio-system get pod -l app=kiali -o jsonpath='{.items[0].metadata.name}') 20001:20001      # Kiali
▒ kubectl -n istio-system port-forward $(kubectl -n istio-system get pod -l app=jaeger -o jsonpath='{.items[0].metadata.name}') 16686:16686     # Jaeger
▒ kubectl -n istio-system port-forward $(kubectl -n istio-system get pod -l app=zipkin -o jsonpath='{.items[0].metadata.name}') 9411:9411       # Zipkin
▒ kubectl -n logging port-forward $(kubectl -n logging get pod -l app=kibana -o jsonpath='{.items[0].metadata.name}') 5601:5601                 # Kibana
~~~

## 프록시

~~~
▒ kubectl  proxy --port=8001
▒ kubectl  proxy --port=8001 --context=acorn-demo
~~~

## API-Server Bearer Token 엑세스

* Proxy 를 통해 접근

~~~
▒ kubectl proxy
▒ TOKEN="$(kubectl get secret $(kubectl get secrets | grep default-token | cut -f1 -d ' ') -o jsonpath='{$.data.token}' | base64 --decode)"
▒ curl -D --insecure --header "Authorization: Bearer $TOKEN" http://localhost:8001/api/v1/namespaces/default/pods
~~~


* ~~client-ca 파일을 활용하여 HTTPS 로 직접 접근~~
~~~
▒ TOKEN="$(kubectl get secret $(kubectl get secrets | grep default-token | cut -f1 -d ' ') -o jsonpath='{$.data.token}' | base64 --decode)"
▒ kubectl get secret -o jsonpath="{.items[?(@.type==\"kubernetes.io/service-account-token\")].data['ca\.crt']}" | base64 --decode > ca.crt
▒ curl --cacert ca.crt -H "Authorization: Bearer $TOKEN" https://101.55.69.109:6443/api/v1/namespaces/default/pods
~~~

## Rollout

* https://kubernetes.io/ko/docs/reference/kubectl/cheatsheet/#리소스-업데이트)


~~~
▒ kubectl set image deployment/frontend www=image:v2               # "frontend" 디플로이먼트의 "www" 컨테이너 이미지를 업데이트하는 롤링 업데이트
▒ kubectl rollout history deployment/frontend                      # 현 리비전을 포함한 디플로이먼트의 이력을 체크 
▒ kubectl rollout undo deployment/frontend                         # 이전 디플로이먼트로 롤백
▒ kubectl rollout undo deployment/frontend --to-revision=2         # 특정 리비전으로 롤백
▒ kubectl rollout status -w deployment/frontend                    # 완료될 때까지 "frontend" 디플로이먼트의 롤링 업데이트 상태를 감시
~~~

## Patch
> Patch 명령으로 CRD 수정 및 추가

```
▒ kubectl patch svc you-svc -p '{"spec": {"type": "NodePort"}}'

▒ kubectl patch pod valid-pod -type='json' -p='[{"op": "replace", "path": "/spec/containers/0/image", "value":"new image"}]'

▒ kubectl patch svc productpage -n bookinfo --type="json" -p '[
  {"op":"replace", "path":"/spec/type",             "value":"NodePort"},
  {"op":"add",     "path":"/spec/ports/0/nodePort", "value":30180 }
]'
```

## Config

* insecure-skip-tls-verify

```
▒ kubectl config set-cluster kubernetes --insecure-skip-tls-verify=true
```

## Dashboard

* Dashboard 배포

~~~
▒ kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.0-beta8/aio/deploy/recommended.yaml
~~~

* Dashboard Service Account `kubernetes-dashboard` 에 cluster-admin 권한 지정

~~~
▒ kubectl apply -f - <<EOF
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: clusterrolebinding-kubernetes-dashboard
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: kubernetes-dashboard
  namespace: kubernetes-dashboard
EOF
~~~

* Dashboard 실행

~~~
▒ kubectl proxy
~~~

* 페이지 오픈 : http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/ 

* 로그인 토큰 조회 & 조회된 token 으로 로그인

~~~
▒ TOKEN=$(kubectl get secret -n kubernetes-dashboard $(kubectl get sa kubernetes-dashboard -n kubernetes-dashboard -o jsonpath={.secrets..name} | cut -f1 -d ' ') -o jsonpath='{$.data.token}' | base64 --decode)
▒ echo $TOKEN
~~~


## Troubleshooting

### Namespace Terminating 지연 이슈

#### 원인
* finalizer 가 정리되지 않음 
  * https://shortstories.gitbook.io/studybook/kubernetes/undefined/kubernetes-namespace-phase-terminating)


### 해결
* 강제 삭제 명령
```
▒ kubectl proxy

# 새로운 터미널창에서

▒ NAMESPACE="istio-system"
▒ kubectl get namespace ${NAMESPACE} -o json > temp.json

# temp.json 파일에서 "finalizers" 값 제거
▒ vi temp.json

▒ curl -k -H "Content-Type: application/json" -X PUT --data-binary @temp.json http://127.0.0.1:8001/api/v1/namespaces/${NAMESPACE}/finalize
▒ rm -f temp.json
```

### Kubectl x509 Unable to Connect

#### 현상

* 클라이언트에서 kubectl  실행시 아래와 같은 메시지 발생
```
Unable to connect to the server: x509: certificate is valid for
```

* 그러나 `insecure-skip-tls-verify` 옵션 처리하면 정상 처리 

```
▒ kubectl --insecure-skip-tls-verify --context=some-context get pods
```

* cluster node 내부에서는 kubectl 정상 실행되고 외부에서만 발생

#### 원인

보통 kubeadm  init한 경우 내부 IP만 certificate authority(CA)가 생성이 되도록만 하고 외부 IP (LB ) 는 반영되도록 하지 않았기 때문에 외부에서 접근할 때 오류 발생


#### 해결
> https://imti.co/kubectl-remote-x509-valid/

`apiserver-cert-extra-sans` 옵션에 외부(공인) IP를 추가하여 해결

```
# remove the certs
▒ rm /etc/kubernetes/pki/apiserver.* 

# re-create with updated --apiserver-cert-extra-sans 
▒ kubeadm init phase certs all --apiserver-advertise-address=0.0.0.0 --apiserver-cert-extra-sans=101.55.69.105

# remove the kubernetes api server container docker 
▒ rm -f `docker ps -q -f 'name=k8s_kube-apiserver*'` 

# restart the kublet 
▒ systemctl restart kubelet
```

## RBAC

```
# default namespace  pod list 권한 여부 확인
$ kubectl auth can-i list pods --as=system:serviceaccount:default:default -n default
```