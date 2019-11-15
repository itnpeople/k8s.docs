# Istio CLI

* istio-ingressgateway HTTP NodePort  가져오기
~~~
▒ kubectl  get svc/istio-ingressgateway -n istio-system -o jsonpath='{.spec.ports[?(@.name=="http2")].nodePort}'
~~~

