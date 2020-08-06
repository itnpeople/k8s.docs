# httpbin
> 테스트용 자작 유틸리티
> [httpbin](https://hub.docker.com/r/kennethreitz/httpbin) 에 필요 유틸 추가



* docker 빌드
~~~
▒ docker build -t honester/httpbin:latest .
▒ docker push honester/httpbin:latest
~~~


* 배포
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
    imagePullPolicy: Always #IfNotPresent
    name: httpbin
~~~


## 사용

* curl
~~~
▒ do kubectl exec -it httpbin -c httpbin -- curl http://svc-hello.default:8080;
▒ for i in {1..20}; do kubectl exec -it httpbin -c httpbin -- curl http://svc-hello.default:8080; sleep 0.1; done
~~~

* dig
~~~
▒ kubectl exec -it httpbin -- dig                                          # 환경설정 조회
▒ kubectl exec -it httpbin -- dig itnp.kr                                  # 도메인 조회
▒ kubectl exec -it httpbin -- dig productpage.default.svc.cluster.local
▒ kubectl exec -it httpbin -- dig @8.8.8.8 google.com              # 특정 네임서버로 조회
~~~

* 리모트 도메인 포트 점검
~~~
▒ kubectl exec -it httpbin -- nslookup productpage.default.svc.cluster.local  # 도메인 점검
▒ kubectl exec -it httpbin -- nmap 10.104.100.127 -p 2379                     # 리모트 포트 점검
▒ kubectl exec -it httpbin -- nc -z daum.net  80                              # 리모트 포트 점검
~~~



## 배포

* master node / worker node에 지정 배포

```
▒ kubectl apply -f - <<EOF
apiVersion: v1
kind: Pod
metadata:
  name: httpbin-master
  labels:
    app: httpbin-master
spec:
  tolerations:
  - key: node-role.kubernetes.io/master
    effect: NoSchedule
  containers:
  - image: docker.io/honester/httpbin:latest
    imagePullPolicy: IfNotPresent
    name: httpbin-node-master
  nodeSelector:
    kubernetes.io/hostname: ip-10-1-3-113
---
apiVersion: v1
kind: Pod
metadata:
  name: httpbin-worker
  labels:
    app: httpbin-worker
spec:
  containers:
  - image: docker.io/honester/httpbin:latest
    imagePullPolicy: IfNotPresent
    name: httpbin-worker
  nodeSelector:
    kubernetes.io/hostname: gcp-instance-1
EOF
---
apiVersion: v1
kind: Service
metadata:
  name: httpbin-master
spec:
  selector:
    app: httpbin-master
  ports:
    - protocol: TCP
      port: 80
---
apiVersion: v1
kind: Service
metadata:
  name: httpbin-worker
spec:
  selector:
    app: httpbin-worker
  ports:
    - protocol: TCP
      port: 80
```

## 성능

### iperf3

```
# server
▒ kubectl exec -it httpbin-master -- ipref3 -s

# client (동시연결 30)
▒ kubectl exec -it httpbin-worker-1 -- iperf3 -P 30 -c httpbin-master
```

