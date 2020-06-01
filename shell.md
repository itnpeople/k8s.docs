# Shell Script


## curl

### HTTPS 요청 with 인증서
~~~
▒ curl -HHost:httpbin.example.com --resolve httpbin.example.com:31390:192.168.0.99 --cacert httpbin.example.com/2_intermediate/certs/ca-chain.cert.pem https://httpbin.example.com:31390
~~~

## cut

### `cut -f1 -d ' '` : ' ' 로 구분 했을 때 첫번째 컬럼
~~~
▒ kubectl get secrets | grep default | cut -f1 -d ' '
~~~


## 리눅스 네트워크 

### nslookup

~~~
▒ nslookup 209.132.183.181  # IP로 도메인 찾기
▒ nslookup redhat.com 8.8.8.8 # 특정 DNS (8.8.8.8 google)를 사용하여 도메인 검색
~~~

### [원격서버 포트 점검](https://zetawiki.com/wiki/리눅스_원격서버_포트_점검)
  * nc (network cat)

~~~
# network cat
▒ nc -z 8.8.8.8 53

# nmap
▒ nmap localhost
▒ nmap ko.wikipedia.org -p 80
~~~

### nmap (network cat)
  * [원격서버 포트 점검](https://zetawiki.com/wiki/리눅스_원격서버_포트_점검)

~~~
▒ nc -z 8.8.8.8 53
~~~


### 로컬 포트 확인 ([losf](https://zetawiki.com/wiki/리눅스_lsof), [netstat](https://zetawiki.com/wiki/리눅스_netstat))

~~~
▒ lsof -i -nP | grep LISTEN | awk '{print $(NF-1)" "$1}' | sort -u
▒ netstat -tnlp
~~~

### 퍼블릭 아이피 얻기 ([dig](https://zetawiki.com/wiki/리눅스_dig))

~~~
# yum install -y bind-utils
▒ dig +short myip.opendns.com @resolver1.opendns.com

▒ curl ifconfig.me
~~~

### ip addr, [ifconfig](https://zetawiki.com/wiki/리눅스_ifconfig)

~~~
# yum install -y net-tools

# public ip로 network interface 생성
▒ ifconfig eth0:1 $(dig +short myip.opendns.com @resolver1.opendns.com) up
▒ ifconfig eth0:1 $(dig +short myip.opendns.com @resolver1.opendns.com) up

# nic 비활성화
▒ ifconfig eth0:1 down

▒ ip addr
~~~

### IP Route

~~~
# routing 조회

▒ route -n
▒ ip route
▒ netstat -rn

# route table에 rule 추가
# 재부팅시 reset, 재부팅시에도 반영되도록 하는 방법은 리눅스 계열별로 참조

▒ route add -net 111.222.33.44 netmask 255.255.255.255 dev eth0	# 111.222.33.44 들어오는 네트워크 요청에 대해 nic eth0 로 응답
▒ route add -net 111.222.33.0 netmask 255.255.255.0 dev eth0	# 111.222.33.* 들어오는 네트워크 요청에 대해 nic eth0 로 응답
▒ route del -net 111.222.33.44 netmask 255.255.255.255 dev eth0 # 삭제
~~~


## 커널

~~~
▒ lsmod | grep 8021q		# 커널에서 vlan 모듈(8021q) 존재 확인
▒ modprobe 8021q			# 커널에 vlan 모듈(8021q) 추가
~~~
