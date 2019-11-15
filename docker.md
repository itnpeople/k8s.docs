# Docker

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