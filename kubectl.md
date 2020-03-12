# kubectl 활용

## POD

* 라벨로 파드명 조회
~~~
▒ kubectl get pod -l app=hello -o jsonpath={.items..metadata.name}
~~~

## Logs

~~~
▒ kubectl logs $(kubectl get pod -l app=hello -o jsonpath={.items..metadata.name}) -c hello-server-v1"
~~~

## Rollout

`kubectl rollout  명령어` (https://kubernetes.io/ko/docs/reference/kubectl/cheatsheet/#리소스-업데이트)


~~~
▒ kubectl set image deployment/frontend www=image:v2               # "frontend" 디플로이먼트의 "www" 컨테이너 이미지를 업데이트하는 롤링 업데이트
▒ kubectl rollout history deployment/frontend                      # 현 리비전을 포함한 디플로이먼트의 이력을 체크 
▒ kubectl rollout undo deployment/frontend                         # 이전 디플로이먼트로 롤백
▒ kubectl rollout undo deployment/frontend --to-revision=2         # 특정 리비전으로 롤백
▒ kubectl rollout status -w deployment/frontend                    # 완료될 때까지 "frontend" 디플로이먼트의 롤링 업데이트 상태를 감시
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

* pod 포트 포워딩
~~~
▒ kubectl port-forward $(kubectl get pod -l app=httpbin -o jsonpath={.items..metadata.name}) 8080:80
~~~

* 어플리케이션별 포트 포워딩 예제
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
▒ kubectl  proxy
▒ TOKEN="$(kubectl get secret $(kubectl get secrets | grep default-token | cut -f1 -d ' ') -o jsonpath='{$.data.token}' | base64 --decode)"
▒ curl -D --insecure --header "Authorization: Bearer $TOKEN" http://localhost:8001/api/v1/namespaces/default/pods
~~~


* ~~client-ca 파일을 활용하여 HTTPS 로 직접 접근~~
~~~
▒ TOKEN="$(kubectl get secret $(kubectl get secrets | grep default-token | cut -f1 -d ' ') -o jsonpath='{$.data.token}' | base64 --decode)"
▒ kubectl get secret -o jsonpath="{.items[?(@.type==\"kubernetes.io/service-account-token\")].data['ca\.crt']}" | base64 --decode > ca.crt
▒ curl --cacert ca.crt -H "Authorization: Bearer $TOKEN" https://101.55.69.109:6443/api/v1/namespaces/default/pods
~~~

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
