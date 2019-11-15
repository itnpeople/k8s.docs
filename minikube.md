# Minikube

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

## API-Server Cross-Domain 허용

~~~
▒ minikube --extra-config apiserver.cors-allowed-origins=.* start
~~~