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

# output 라인 갯수
▒ kubectl get nodes | awk 'END { print NR }'
```


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
▒ sudo bash -c 'cat > /etc/docker/daemon.json <<EOF
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

* 작은 따옴표안에서 작은 따옴표 문자열 사용 : `\047`
~~~
▒ echo -e 'kubectl get nodes --no-headers | awk \047END { print NR }\047'

kubectl get nodes --no-headers | awk 'END { print NR }'
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

* /etc/hosts 파일에서 `127.0.0.1` 로 시작 하는 행을 `127.0.0.1 localhost itnp.kr` 로 변경
```
▒ sed -i "s/127.0.0.1.*/127.0.0.1 localhost itnp.kr/g" /etc/hosts
```

* 문자열 `aaaa` 를  `bbbb` 로 replace

```
▒ echo "aaaa.bbbb.cccc" | sed -e "s/aaaa/bbbb/g"

bbbb.bbbb.cccc
```

* 큰따옴표를 제거 Replace - "\"를 앞에 붙여줌

```
▒ echo "\"cb-cluster-w-xzuz4\"" | sed -e "s/\"//g"

cb-cluster-w-xzuz4
```