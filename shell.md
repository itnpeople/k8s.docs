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




TOKEN="$(k get secret $(k get secrets | grep default-token | cut -f1 -d ' ') -o jsonpath='{$.data.token}' | base64 --decode)"