# Minikube

##  시작(생성)

```
▒ minikube start
```

* api-server Cross-Domain 허용

~~~
▒ minikube --extra-config apiserver.cors-allowed-origins=.* start
~~~


## Addons

```
▒ minikube addons list

▒ minikube addons enable metrics-server
```

## 터널링

* 터널링 시작
~~~
▒ minikube tunnel
▒ sudo route -n add 10.0.0.0/8 $(minikube ip)
~~~

* 터널링 종류
~~~
▒ sudo route -n delete 10.0.0.0/8
▒ minikube tunnel --cleanup
~~~


## Troubleshooting

* `minikube start` > `minikube api server version match failed: server version: the server has asked for the client to provide credentials`
  * 아래와 같이 이전 profile 을 모두 삭제하고 재 시작
```
▒ minikube delete --all --purge
▒ minikube start
```
