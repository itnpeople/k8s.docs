# httpbin
> 테스트용 자작 유틸리티
> [httpbin](https://hub.docker.com/r/kennethreitz/httpbin) 에 필요 유틸 추가



* Dockerfile
~~~
FROM kennethreitz/httpbin:latest

RUN apt-get update
RUN apt-get install curl iputils-ping-y
~~~

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
▒ exec -it httpbin -- dig                                          # 환경설정 조회
▒ exec -it httpbin -- dig itnp.kr                                  # 도메인 조회
▒ exec -it httpbin -- dig productpage.default.svc.cluster.local
▒ kubectl exec -it httpbin -- dig @8.8.8.8 google.com              # 특정 네임서버로 조회
~~~
