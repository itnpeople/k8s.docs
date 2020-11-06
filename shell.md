# Shell Script


## curl

* HTTPS 요청 with 인증서
~~~
▒ curl -HHost:httpbin.example.com --resolve httpbin.example.com:31390:192.168.0.99 --cacert httpbin.example.com/2_intermediate/certs/ca-chain.cert.pem https://httpbin.example.com:31390
~~~


* 기타 사용법
~~~
▒ curl -s https://httpbin.org/headers -X GET                                     # http methods (GET,POST,PUT,DELETE)
▒ curl -s https://httpbin.org/headers -H "Header1:value1" -H "Header2:value2"    # add http header
▒ curl -s https://httpbin.org/headers -o /dev/null                               # output empty
▒ curl -s https://httpbin.org/headers -o /dev/null -w "code:%{http_code}";       # response code
▒ curl -i https://httpbin.org/ip                                                 # -i : print with http-headers
~~~

## 문자열 조작

### cut
>  구분자로 문자열 추출

* `cut -f1 -d ' '` : ' ' 로 구분 했을 때 첫번째 컬럼

~~~
▒ kubectl get secrets | grep default | cut -f1 -d ' '
~~~

### awk
> 문자열 처리

```
# 2행 1번째 컬럼 값 
▒ kubeadm token list | awk 'FNR==2 {print $1}'
```


## Network

### nslookup

~~~
▒ nslookup 209.132.183.181  # IP로 도메인 찾기
▒ nslookup redhat.com 8.8.8.8 # 특정 DNS (8.8.8.8 google)를 사용하여 도메인 검색
~~~

### 원격서버 포트 점검

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


### 로컬 포트 확인 

* [losf](https://zetawiki.com/wiki/리눅스_lsof)
~~~
▒ lsof -i -nP | grep LISTEN | awk '{print $(NF-1)" "$1}' | sort -u
~~~

* [netstat](https://zetawiki.com/wiki/리눅스_netstat))
~~~
▒ netstat -tnlp
~~~

### 퍼블릭 아이피 얻기

* [dig](https://zetawiki.com/wiki/리눅스_dig)

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

### IP routing 조회

~~~
▒ route -n
▒ ip route
▒ netstat -rn

# route table에 rule 추가
# 재부팅시 reset, 재부팅시에도 반영되도록 하는 방법은 리눅스 계열별로 참조

▒ route add -net 111.222.33.44 netmask 255.255.255.255 dev eth0	# 111.222.33.44 들어오는 네트워크 요청에 대해 nic eth0 로 응답
▒ route add -net 111.222.33.0 netmask 255.255.255.0 dev eth0	# 111.222.33.* 들어오는 네트워크 요청에 대해 nic eth0 로 응답
▒ route del -net 111.222.33.44 netmask 255.255.255.255 dev eth0 # 삭제

▒ traceroute 10.90.1.11 # routing trace
~~~

메인 network interface 이름 얻기
~~~
▒ ip route get 8.8.8.8 | awk '{ print $5; exit }'
~~~


## 커널

~~~
▒ lsmod | grep 8021q  # 커널에서 vlan 모듈(8021q) 존재 확인
▒ modprobe 8021q      # 커널에 vlan 모듈(8021q) 추가
~~~

## 파일 조작

### 파일 생성

* echo 명령으로 파일 만들기

~~~
▒  echo -e "[Interface]
Address = 10.66.66.1/24
ListenPort = 51820
PrivateKey = $(sudo wg genkey)" | sudo tee /etc/wireguard/wg0.conf
~~~

* cat 으로 파일 생성 sudo
~~~
▒  sudo bash -c 'cat > /etc/docker/daemon.json <<EOF
{
  "exec-opts": ["native.cgroupdriver=systemd"],
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "100m"
  },
  "storage-driver": "overlay2"
}
EOF'
~~~

* cat 으로 파일 생성 sudo
~~~
▒ sudo cat <<EOF> /etc/docker/daemon.json
{
  "exec-opts": ["native.cgroupdriver=systemd"],
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "100m"
  },
  "storage-driver": "overlay2"
}
EOF
~~~

### 파일 LF(line feed) 문자열 변환

* 파일(gcp.key)을 라인별로 읽어서 "\n" 붙여 문자열로 만들기
~~~
▒ while read line; do
	if [[ "$line" != "" ]]; then
		V_PKEY="$V_PKEY$line\n";
	fi
done < "gcp.key"

▒ echo $V_PKEY
~~~

* 파일을 읽어  LF(line feed) 를 "\n" 로 전환한 문자열로 변환
~~~
# 방법 1
▒ while read line; do if [[ "$line" != "" ]]; then echo -n "$line\n";fi; done < aaa.txt

# 방법 2
▒ cat aaa.txt | while read line; do if [[ "$line" != "" ]]; then echo -n "$line\n";fi; done
~~~


### 파일 수정 `tee`

* 파일에 행 추가
~~~
▒  echo "set \$l7 ${COMMAND};" | sudo tee -a /usr/local/nginx/conf/nginx-test.conf"
~~~

### 파일 문자열(행) 추가, 변환, 제거 `sed`

* admin.conf 파일에서 `server:` 로 시작하는 행에서 `server: (none)` 행을 추가하고 5번행을 삭제해 출력한다.
~~~
▒ sed -e '/server:/i\server: (none)' -e '5d'  admin.conf
~~~

* admin.conf 파일에서 5번행을  `    server: https://100.100.100.0:6443` 로 교체해서 출력
~~~
▒ sed '5s/.*/    server: https:\/\/100.100.100.0:6443/g' /etc/kubernetes/admin.conf
~~~

* credentials 파일 2행에서 `aws_secret_access_key = ` 문자열을 제거하고 출력
~~~
▒ head -n 2 credentials | tail -n 1 | sed  's/aws_secret_access_key = //g'
~~~


## 기타

* ubuntu 호스트명 변경
~~~
▒  hostnamectl set-hostname "cb-k8s-master" && exec bash
~~~

* Linux `reboot` 명령이  hang 걸렸을 경우 대처 방법 [원본](https://unix.stackexchange.com/questions/442932/reboot-is-not-working)

~~~
▒ echo 1 > /proc/sys/kernel/sysrq
▒ echo b > /proc/sysrq-trigger
~~~
