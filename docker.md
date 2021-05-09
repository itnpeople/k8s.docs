# Docker

## Build

* 빌드
~~~
▒ docker build -t honester/apps:latest .
~~~

* 태그

```
▒ docker tag honester/apps:latest honester/apps:0.1.0 
```

## Docker hub 

* 로그인

```
▒ docker login -u honester -p <password>
```

* 푸쉬
~~~
▒ docker push honester/apps:latest
▒ docker push honester/apps:0.1.0
~~~


## Run

* link 옵션 : 컨테이너간 hostname network 연결

```
▒ docker run -d -p 1024:1024 --name cb-spider cloudbaristaorg/cb-spider:v0.2.0-20200715
▒ docker run -d -p 1323:1323 --name cb-tumblebug --link cb-spider:cb-spider cloudbaristaorg/cb-tumblebug:v0.2.0-20200715
```

* hostPath 볼륨 연결  (v 옵션 )

```
# <host path>:<container path>

▒ docker run --rm -d -p 3001:3001 -v ${HOME}/.kube/config:/app/kube-config --name backend acornsoftlab/acornsoft-dashboard.backend:latest
```

* environment (e 옵션)

```
▒ docker run --rm -d -p 3001:3001 -e BACKEND_PORT=3001 -v ${HOME}/.kube/config:/app/kube-config --name backend acornsoftlab/acornsoft-dashboard.backend:latest
```


## Toubleshooting

* Docker error : no space left on device

```
▒ docker system prune
```
