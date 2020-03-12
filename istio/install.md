# istio 설치방법


## 1.2.4

* 다운로드 +  네임스페이스 생성
~~~
▒ cd install/istio/
▒ curl -L https://git.io/getLatestIstio | ISTIO_VERSION=1.2.4 sh -
▒ kubectl create ns istio-system
~~~

* CRDs 생성
~~~
▒ helm template istio-1.2.4/install/kubernetes/helm/istio-init --name istio-init --namespace istio-system | kubectl apply -f -
~~~

* 설치
~~~
▒ helm template istio-1.2.4/install/kubernetes/helm/istio --name istio --namespace istio-system \
--set gateways.istio-ingressgateway.type=NodePort \
| kubectl apply -f -
~~~


## Values

* Kiali 함께 설치
  * `--set kiali.enabled=true` 

* 프로메테우스 신규 설치 하지 않고 다른 프로메테우스 사용
  * `--set prometheus.enabled=false`
  * `--set prometheusAddr=http://prometheus.cocktail-addon.svc.cluster.local:80`



### external 프로메테우스 사용시
> 기존 운영중인 프로메테우스에서 istio metrics 수집 처리하도록 configmap 수정 작업

* 아래와 같이 configmap 에 `istio-mesh`, `istio-telemetry` job 추가

~~~
▒ kubectl edit configmap prometheus -n cocktail-addon

    - job_name: 'istio-mesh'
      kubernetes_sd_configs:
      - role: endpoints
        namespaces:
          names:
          - istio-system
      relabel_configs:
      - source_labels: [__meta_kubernetes_service_name, __meta_kubernetes_endpoint_port_name]
        action: keep
        regex: istio-telemetry;prometheus

    - job_name: 'istio-telemetry'
      kubernetes_sd_configs:
      - role: endpoints
        namespaces:
          names:
          - istio-system
      relabel_configs:
      - source_labels: [__meta_kubernetes_service_name, __meta_kubernetes_endpoint_port_name]
        action: keep
        regex: istio-telemetry;http-monitoring
~~~
