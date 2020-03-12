# httpbin chart (devel.)

## 배포 테스트

* helm template 명령으로 배포 테스트

```
▒ helm template httpbin httpbin --namespace default | kubectl apply -f -
```

*  포트 포워딩을 사용하여 접속 확인 (http://localhost:8080 , loadBalancer가 없는 on-premise)

```
▒ kubectl port-forward $(kubectl get pod -l app=httpbin -o jsonpath={.items..metadata.name}) 8080:80
```

* external ip 확인 (public cloud ) 

```
▒ echo http://$(kubectl get svc/httpbin -o jsonpath={.status.loadBalancer.ingress[0].ip})
```

## helm 3 패키징

* 패키징 (httpbin-devel.tgz) 파일 생성

```
▒ helm package httpbin/
```

* github push

```
▒ git add *
▒ git commit -m 'ADD httpbin helm chart'
▒ git push origin master
```

## Install

```
▒ helm install httpbin-a https://raw.githubusercontent.com/itnpeople/k8s.docs/master/charts/httpbin-devel.tgz
```


## install (context 별)


* context apps-05 에 설치

```
▒ helm install httpbin-a https://raw.githubusercontent.com/itnpeople/k8s.docs/master/charts/httpbin-devel.tgz --kube-context apps-05
```
* context apps-05 에 확인
```
▒ kubectl get pod -n default --context apps-05| grep httpbin

httpbin-9f6dd8548-9jt9x           1/1     Running   0          59s
```


* context escho-cluster-admin 에 설치

```
▒ helm install httpbin-b https://raw.githubusercontent.com/itnpeople/k8s.docs/master/charts/httpbin-devel.tgz --kube-context escho-cluster-admin
```

* context escho-cluster-admin 조회

```
▒ kubectl get pod -n default --context escho-cluster-admin | grep httpbin

httpbin-9f6dd8548-8qnfm             1/1     Running   0          31s
```