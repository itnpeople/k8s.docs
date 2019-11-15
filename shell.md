# Shell Script


## curl

* HTTPS 요청 with 인증서
~~~
▒ curl -HHost:httpbin.example.com --resolve httpbin.example.com:31390:192.168.0.99 --cacert httpbin.example.com/2_intermediate/certs/ca-chain.cert.pem https://httpbin.example.com:31390
~~~

## cut

* `cut -f1 -d ' '` : ' ' 로 구분 했을 때 첫번째 컬럼
~~~
▒ kubectl get secrets | grep default | cut -f1 -d ' '
~~~


## 네트워크

* nslookup
~~~
▒ kubectl exec -n default -ti busybox -- nslookup kubernetes.default
~~~

* [리눅스 원격서버 포트 점검](https://zetawiki.com/wiki/리눅스_원격서버_포트_점검)
  * nc (network cat)
  * TCP나 UDP 프로토콜을 사용하는 네트워크 연결에서 데이터를 읽고 쓰는 간단한 유틸리티
~~~
▒ nc -z 8.8.8.8 53
▒ nc echo -e "HEAD / HTTP/1.0\n\n" | nc httpd.apache.org 80
~~~

* 로컬 포트 확인
~~~
▒ lsof -i -nP | grep LISTEN | awk '{print $(NF-1)" "$1}' | sort -u
~~~