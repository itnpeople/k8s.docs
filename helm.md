# Helm

* https://github.com/helm/charts

## Install

### Repository 를 통한 설치

* Repository 추가
```
▒ helm repo add stable https://kubernetes-charts.storage.googleapis.com/
▒ helm repo update
```

* chart 검색 및 설치

```
# 검색
▒ helm search repo nginx

stable/nginx-ingress            1.33.5          0.30.0          An nginx Ingress controller that uses ConfigMap...
stable/nginx-ldapauth-proxy     0.1.3           1.13.5          nginx proxy with ldapauth                         
stable/nginx-lego               0.3.1                           Chart for nginx-ingress-controller and kube-lego  
stable/gcloud-endpoints         0.1.2           1               DEPRECATED Develop, deploy, protect and monitor...

# 설치
▒ helm install nginx-devel stable/nginx-ingress

# 조회
▒ helm list
```

### Chart 파일 (.tgz) 지정 설치

```
▒ helm install release-devel https://raw.githubusercontent.com/itnpeople/k8s.docs/master/charts/httpbin-devel.tgz
```


## Github을 Repository로 구성하기

*  helm chart의 tgz 파일 생성 helm package
```
▒ helm package <CHART_PATH>    # packaging  (tgz file 생성)
▒ helm repo index .            # index.yaml 파일 생성/수정
```

*  index.yaml 생성 - Url 지정하는 경우
```
▒ helm repo index --url "https://itnpeople.github.io/helmcharts" .
```

*  생성한  index.yaml, tgz 파일 push
```
▒ git add .
▒ git commit -m "feat : Helm Chart" 
▒ git push origin master
```

## Development

### Install 정상동작 여부 확인
* `--dry-run`,  `--debug` 옵션 사용

```
▒ helm install --dry-run --debug release-devel . --set service.internalPort=8080
```

###  Yaml로 출력해보기

```
▒ helm template --debug release-devel  . --set service.internalPort=8080
```

### Template - default value

```
          persistentVolumeClaim:
            claimName: {{ default "acornsoft-dashboard-metrics-scraper"  .Values.metricsScraper.persistence.existingClaim }}
```

### Accessing Files Inside Templates

* 공식문서 : https://helm.sh/docs/chart_template_guide/accessing_files
* 사용예 : https://github.com/bitnami/charts/blob/master/bitnami/influxdb/templates/influxdb/configmap.yaml


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

## Sub-chart
> Chart.yaml(recommended, 또는 requirements.yaml) `dependencis` 속성에 지정된 dependency charts

* list : https://helm.sh/docs/helm/helm_dependency_list/
* build : https://helm.sh/docs/helm/helm_dependency_build/
* update : https://helm.sh/docs/helm/helm_dependency_update/

```
▒ helm dep list    # dependency 리스트 와 상태 조회
▒ helm dep build   # dependency sub-chart charts 빌드
▒ helm dep up      # dependency sub-chart charts 업데이트
```

### Sub-chart 에 대한 속성 지정

* `values.yaml` 파일에  **Hierarchy** 구조로 지정 가능


* 아래는 sub-chart로 정의된 `grafana` chart의  `testFramework.enabled` Configuration 속성 값을 `false` 로 지정하는 예제

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

## Hook : 릴리스 수명주기의 특정 지점에 동작 처리 (전처리, 후처리)
> https://helm.sh/ko/docs/topics/charts_hooks/

* pre-install, post-install
* pre-delete, post-delete
* pre-upgrade, post-upgrade
* pre-rollback, post-rollback
* test