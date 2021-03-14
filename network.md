# Network

## Loadbalancer

* L2 (Data Link layer) : **Mac**
* L3 (Network layer),  : **IP**
* L4 (Transport layer) : **IP + Port**
* L7 (Application layer) : **IP + Port + 패킷**


## ubuntu 호스트명 변경
~~~
▒  hostnamectl set-hostname "cb-k8s-master" && exec bash
~~~

* Linux `reboot` 명령이  hang 걸렸을 경우 대처 방법 [원본](https://unix.stackexchange.com/questions/442932/reboot-is-not-working)

~~~
▒ echo 1 > /proc/sys/kernel/sysrq
▒ echo b > /proc/sysrq-trigger
~~~


## nslookup

~~~
▒ nslookup 209.132.183.181  # IP로 도메인 찾기
▒ nslookup redhat.com 8.8.8.8 # 특정 DNS (8.8.8.8 google)를 사용하여 도메인 검색
~~~

## 원격서버 포트 점검

* nc (network cat)
~~~
# network cat
▒ nc -z 8.8.8.8 53
~~~

* nmap
~~~
# nmap
▒ nmap localhost
▒ nmap ko.wikipedia.org -p 80
~~~

## 로컬 포트 확인 

* [losf](https://zetawiki.com/wiki/리눅스_lsof)
~~~
▒ lsof -i -nP | grep LISTEN | awk '{print $(NF-1)" "$1}' | sort -u
~~~

* [netstat](https://zetawiki.com/wiki/리눅스_netstat))
~~~
▒ netstat -tnlp
~~~

## 퍼블릭 아이피 얻기

* [dig](https://zetawiki.com/wiki/리눅스_dig)

~~~
# yum install -y bind-utils
▒ dig +short myip.opendns.com @resolver1.opendns.com

▒ curl ifconfig.me
~~~

## ip addr, [ifconfig](https://zetawiki.com/wiki/리눅스_ifconfig)

~~~
# yum install -y net-tools

# public ip로 network interface 생성
▒ ifconfig eth0:1 $(dig +short myip.opendns.com @resolver1.opendns.com) up
▒ ifconfig eth0:1 $(dig +short myip.opendns.com @resolver1.opendns.com) up

# nic 비활성화
▒ ifconfig eth0:1 down

▒ ip addr
~~~

* Get IP Address
```
▒ ip -o route get 8.8.8.8 | sed -e 's/^.* src \([^ ]*\) .*$/\1/'

172.20.0.2
```

* 메인 network interface 이름 얻기
~~~
▒ ip route get 8.8.8.8 | awk '{ print $5; exit }'

eth0
~~~

## ip 명령어

```
▒ ip addr show                                            # ip 정보 출력
▒ ip addr add 192.168.5.10/24 dev eth0                    # eth0 인터페이스에 ip 설정
▒ ip addr del 192.168.5.10/24 dev eth0                    # eth0 인터페이스의 ip 삭제
▒ ip link set eth0 up                                     # eth0 인터페이스 활성화
▒ ip link set eth0 down                                   # eth0 인터페이스 비활성화
▒ ip route show                                           # 라우팅 정보 출력
▒ ip route add default via 192.168.5.1                    # 게이트웨이 설정
▒ ip route del default via 192.168.5.1                    # 게이트웨이 삭제
▒ ip route add 10.20.12.0/24 via 192.168.5.1 dev eth0     # 정적 라우팅 정보 설정
▒ ip route del 10.20.12.0/24                              # 정적 라우팅 정보 삭제
```

## 라우팅 테이블

* 조회

```
▒ route -n
▒ ip route
▒ netstat -rn
```

* Trace
```
▒ traceroute 10.90.1.11 # routing trace

# 설치
▒ apt-get install traceroute
```

* 추가
  * `route add` 명령으로 route table에 rule 추가 가능
  * 재부팅시 reset, 재부팅시에도 반영되도록 하는 방법은 리눅스 계열별로 참조


* 삭제
```
▒ route del -net 111.222.33.44 netmask 255.255.255.255 dev eth0 # 삭제
```

### Use Case

* 111.222.33.44, 111.222.33.* 들어오는 네트워크 요청에 대해 nic eth0 로 응답
```
▒ route add -net 111.222.33.44 netmask 255.255.255.255 dev eth0	
▒ route add -net 111.222.33.0 netmask 255.255.255.0 dev eth0
```


## NAT (iptable)

* Chain
  * PREROUTING :외부로부터 들어오는 트래픽
  * POSTROUTING :외부로 나가는 트래픽
  * OUTPUT : 로컬에서 발생해 외부로 나가는 트래픽(패킷)

* 조회

```
▒ iptables -nL -t nat               # 조회
▒ iptables -nL PREROUTING -t nat    # DNAT 조회 
▒ iptables -nL POSTROUTING -t nat   # SNAT 조회
```

* SNAT 추가 
  * `eth0:1` NIC으로  라우팅 될 때 Source를 `172.10.0.92`로 지정한다.
  * `-t nat` : nat 테이블에 규칙을 추가한다.
  * `-j SNAT` : 사용할 기능으로, DNAT 또는 SNAT, MASQUERADE를 명시할 수 있다.
  * `-o eth0:1` : eth0:1로 나가는(out) 트래픽에 대해서 적용한다.
  * `--to 172.10.0.92` : 변경되어 최종적으로 패킷에 설정될 Source 주소

```
▒ iptables -t nat -A POSTROUTING -o eth0:1  -j SNAT --to 172.10.0.92
```

* 삭제
```
▒ iptables -nL POSTROUTING -t nat  --line-number
▒ iptables -t nat -D POSTROUTING 7                   # 7은 라인번호(num)
```
### Use Case

* `<ip-1>`로 나가는 패킷의 source ip를  `<ip-2>` 로 변환
```
▒ iptables -t nat -A POSTROUTING -d <ip-1> -j SNAT --to-source <ip-2>
```

* `<ip>:80`로 들어오는 패킷을 `<ip>:81` 로 라우팅 
  * 외부에서 들어오는 패킷 (PREROUTING)과 로컬 발생 패킷(OUTPUT) 을 동시에 지정
```
▒ iptables -A PREROUTING -t nat -j DNAT -p tcp -d <ip> --dport 80 --to-destination <ip>:81
▒ iptables -A OUTPUT -t nat -j DNAT -p tcp -d <ip> --dport 80 --to-destination <ip>:81
```

* `<ip>:80`로 들어오는 패킷을 `<ip>:81` 로 라우팅 (DNAT 대신 REDIRECT 활용하는 방법)
  * dhcp 처럼 dynamic IP 환경에서 활용가능
```
▒ iptables -A PREROUTING -t nat -i eth0 -p tcp --dport 80 -j REDIRECT --to-port 81
```

* `<ip>`로 inboud/outbound 패킷을 제거
```
▒ iptables -A INPUT -s <ip> -j DROP    #inbound
▒ iptables -A OUTPUT -d <ip> -j DROP   #outbound
```

* `tcp` `<port>`로 들어오는 패킷 허용
```
▒ iptables -A INPUT -p tcp --dport <port> -j ACCEPT
```

* 54.238.87.186 (local) 에서 '18.180.118.194'로 나가는 패킷을 로컬 8080 포트로 redirect 한다.
```
▒ iptables -t nat -I OUTPUT --src 54.238.87.186 --dst 18.180.118.194 -p tcp --dport 8080 -j REDIRECT --to-ports 8080
```

* '18.180.118.194'로 나가는 패킷을 "172.10.0.134"로 변경한다.
```
▒ iptables -t nat -A OUTPUT -d 18.180.118.194 -j DNAT --to-destination 172.10.0.134
```

### tcpdump

* `ping`으로 source-ip, destination-ip 확인 가능

```
▒ tcpdump icmp -n
```

* if eth0 를 통해 source-ip "172.10.0.191" 에서 destionation-ip "54.248.191.2" 8080 포트로 가는 패킷 덤프
```
▒  tcpdump -i eth0 src 172.10.0.191 and port 8080 and dst 54.248.191.23
```


## 참조
![Kubernetes 네트워크](https://miro.medium.com/max/1400/1*qq0ioT5k9eazkHH402h3mw.png)
* [출처 - Kubernetes 네트워크 정리](https://sookocheff.com/post/kubernetes/understanding-kubernetes-networking-model/)
