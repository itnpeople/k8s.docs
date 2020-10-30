# Docker

## Build

* latest로만 빌드
~~~
▒ docker build -t honester/servicemesh-prototype:latest .
▒ docker push honester/servicemesh-prototype:latest
~~~

* 버전 빌드, 버전 & latest 푸쉬
~~~
▒ docker build -t honester/servicemesh-prototype:0.1.0 .
▒ docker tag honester/servicemesh-prototype:0.1.0 honester/servicemesh-prototype:latest
▒ docker login
▒ docker push honester/servicemesh-prototype:0.1.0
▒ docker push honester/servicemesh-prototype:latest
~~~

* docker hub login (using id, password)

```
▒ docker login -u acornsoftlab -p <password>
```

## Run

* link 옵션 : 컨테이너간 hostname network 연결

```
▒ docker run -d -p 1024:1024 --name cb-spider cloudbaristaorg/cb-spider:v0.2.0-20200715
▒ docker run -d -p 1323:1323 --name cb-tumblebug --link cb-spider:cb-spider cloudbaristaorg/cb-tumblebug:v0.2.0-20200715
```

* hostPath 볼륨 연결  (v 옵션 )

```
# <host path>:<container path>

▒ docker run --rm -d -p 3001:3001 -v ${HOME}/.kube/config:/app/kube-config --name backend acornsoftlab/kore3.backend:latest
```

## Toubleshooting

* Docker error : no space left on device

```
▒ docker system prune
```
