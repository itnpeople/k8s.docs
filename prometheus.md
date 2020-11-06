# Prometheus

## Configuration
* https://prometheus.io/docs/prometheus/latest/configuration/configuration/

### Reload configuration

```
$ kubectl port-forward svc/prometheus 9090
$ curl -X POST http://localhost:9090/-/reload
```

## Admin API Enable
* https://prometheus.io/docs/prometheus/latest/querying/api/
* Default로 disabled 되어 있으므로 Admin API 를 사용하기 위해서는 prometheus deployment args 에 `--web.enable-admin-api` 옵션 추가

```
....

  containers:
  - args:
    - --storage.tsdb.retention.time=15d
    - --config.file=/etc/config/prometheus.yml
    - --storage.tsdb.path=/data
    - --web.console.libraries=/etc/prometheus/console_libraries
    - --web.console.templates=/etc/prometheus/consoles
    - --web.enable-lifecycle
    - --web.enable-admin-api
    image: prom/prometheus:v2.19.0
....
```

###  Clean-up metrics data
* 아래 Admin-API를 요청하면 데이터 삭제
  * `POST /api/v1/admin/tsdb/clean_tombstones`
  * `PUT /api/v1/admin/tsdb/clean_tombstones` 
* HTTP response status code 가 204로 리턴되면 성공
* 실행 예

```
$ curl -XPOST http://localhost:9090/api/v1/admin/tsdb/clean_tombstones
```
