# kubectl 활용


##  테스트용 Pod

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

* 예
~~~
▒ kubectl port-forward svc/productpage -n default 9080                                         # 일반
▒ kubectl port-forward --context acorn-rnd svc/productpage -n default 9080                     # Context 지정
▒ kubectl port-forward --context acorn-rnd svc/productpage -n default 9080 > /dev/null         # 출력 없음
▒ kubectl port-forward --context acorn-rnd svc/productpage -n default 9080 2> /dev/null        # 출력 & 에러 없음
▒ kubectl port-forward --context acorn-rnd svc/productpage -n default 9080 > /dev/null 2>&1 &  # 출력 & 에러 + 데몬
~~~

* 어플리케이션별
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

## 디버깅

~~~
▒ kubectl get pods --v=7
~~~

~~~
--v=0	일반적으로 클러스터 운영자(operator)에게 항상 보여지게 하기에는 유용함.
--v=1	자세한 정보를 원하지 않는 경우, 적절한 기본 로그 수준.
--v=2	서비스와 시스템의 중요한 변화와 관련이있는 중요한 로그 메시지에 대한 유용한 정상 상태 정보. 이는 대부분의 시스템에서 권장되는 기본 로그 수준이다.
--v=3	변경 사항에 대한 확장 정보.
--v=4	디버그 수준 상세화.
--v=6	요청한 리소스를 표시.
--v=7	HTTP 요청 헤더를 표시.
--v=8	HTTP 요청 내용을 표시.
--v=9	내용을 잘라 내지 않고 HTTP 요청 내용을 표시.
~~~