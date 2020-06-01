# Helm

* https://github.com/helm/charts


## Repository

* repository 추가
  * stable, incubator : https://hub.helm.sh/charts

```
▒ helm repo add stable https://kubernetes-charts.storage.googleapis.com/
▒ helm repo add incubator https://kubernetes-charts-incubator.storage.googleapis.com/
▒ helm repo update
```

* repoistory에서 chart 검색 및 설치

```
# 검색
▒ helm search repo nginx

stable/nginx-ingress            1.33.5          0.30.0          An nginx Ingress controller that uses ConfigMap...
stable/nginx-ldapauth-proxy     0.1.3           1.13.5          nginx proxy with ldapauth                         
stable/nginx-lego               0.3.1                           Chart for nginx-ingress-controller and kube-lego  
stable/gcloud-endpoints         0.1.2           1               DEPRECATED Develop, deploy, protect and monitor...

# 설치
▒ helm install release-devel stable/nginx-ingress

# 조회
▒ helm list
```

## development

```
# install
▒ helm install --dry-run --debug release-devel . --set service.internalPort=8080

# template (yaml 출력)
▒ helm template --debug release-devel  . --set service.internalPort=8080
```

* Template `Files.Glob`
  * https://helm.sh/docs/chart_template_guide/accessing_files/
  * https://github.com/bitnami/charts/blob/master/bitnami/influxdb/templates/influxdb/configmap.yaml

```
data:
  app-conf.yaml: |-
    {{ .Files.Get "files/conf/app-config.yaml" | indent 4 }}

```
```
  {{- $files := .Files }}
  {{- range tuple "config1.toml" "config2.toml" "config3.toml" }}
  {{ . }}: |-
    {{ $files.Get . }}
  {{- end }}
```

```
data:
{{ (.Files.Glob "files/conf/influxdb.conf").AsConfig | indent 2 }}
```



### dependency

* sub-chart 조회
  * 현재 디렉토리 위치의 chart 
  * Chart.yaml(recommended, 또는 requirements.yaml) `dependencis` 속성에 지정된 dependency charts(sub-charts)
  * list : https://helm.sh/docs/helm/helm_dependency_list/
  * build : https://helm.sh/docs/helm/helm_dependency_build/
  * update : https://helm.sh/docs/helm/helm_dependency_update/

```
▒ helm dep list    # dependency 리스트 와 상태 조회
▒ helm dep build   # dependency sub-chart charts 빌드
▒ helm dep up      # dependency sub-chart charts 업데이트
```

* dependency chart(sub-chart) 속성 지정 - `values.yaml`

  * sub-chart로 정의된 `grafana` 의 `testFramework.enabled` Configuration 값을 `false` 로 지정하는 예제

```
# Chart..yaml

dependencies:
  - name: grafana
    version: ~5.0.25
    repository: "https://kubernetes-charts.storage.googleapis.com"
    condition: grafana.enabled

# values.yaml

grafana:
  enabled: true
  testFramework:
    enabled: false
```

