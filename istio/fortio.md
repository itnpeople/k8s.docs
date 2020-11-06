# Fortio

* 실행
~~~
▒ kubectl exec -it fortio -c fortio /usr/bin/fortio -- load -c 10 -qps 0 -n 2000 -loglevel Error http://svc-hello.default:8080
~~~