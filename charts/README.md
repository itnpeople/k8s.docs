# httpbin chart (devel.)

## Install

```
▒ helm install release-devel https://raw.githubusercontent.com/itnpeople/k8s.docs/master/charts/httpbin-devel.tgz

# context apps-05 에 배포
▒ helm install release-devel https://raw.githubusercontent.com/itnpeople/k8s.docs/master/charts/httpbin-devel.tgz --kube-context apps-05

# 배포 확인
▒ kubectl get pod -n default --context apps-05| grep httpbin

httpbin-9f6dd8548-9jt9x           1/1     Running   0          59s
```

## development

### 배포 테스트

* helm template 명령으로 배포 테스트

```
▒ helm template httpbin . --namespace default | kubectl apply -f -
```

*  포트 포워딩을 사용하여 접속 확인 (http://localhost:8080 , loadBalancer가 없는 on-premise)

```
▒ kubectl port-forward $(kubectl get pod -l app=httpbin -o jsonpath={.items..metadata.name}) 8080:80
```

* external ip 확인 (public cloud ) 

```
▒ echo http://$(kubectl get svc/httpbin -o jsonpath={.status.loadBalancer.ingress[0].ip})
```

### helm 3 패키징

* 패키징 (httpbin-devel.tgz) 파일 생성

```
▒ helm package .
```

```
# install
▒ helm install release-devel httpbin-devel.tgz

# install 확인
▒ helm list
NAME         	NAMESPACE    	REVISION    UPDATED                                 STATUS  	CHART           APP VERSION
release-devel	default	        1           2020-05-20 15:37:50.589253 +0900 KST    deployed    httpbin-devel   1.0.0
```

* 패키징 파일 github으로 push
```
▒ git add *
▒ git commit -m 'ADD httpbin helm chart'
▒ git push origin master
```