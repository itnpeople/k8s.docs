# kubeadm


## Provisioning

* Token
~~~
# 현재 token 리스트
▒  kubeadm token list 

# control-plane endpoint 확인
▒ END_POINT=$(echo "$(kubeadm config view | grep controlPlaneEndpoint)" | awk '{ print $2}')

#  token 생성
▒ TOKEN=$(kubeadm token create)
▒ HASH=$(openssl x509 -pubkey -in /etc/kubernetes/pki/ca.crt | openssl rsa -pubin -outform der 2>/dev/null | openssl dgst -sha256 -hex | sed 's/^.* //')

# join (on worker node)
kubeadm join <END_POINT> --token <TOKEN> --discovery-token-ca-cert-hash sha256:<HASH>
~~~

## Join

* 현재 token 조회

```
▒ kubeadm token list 
▒ kubeadm token list | awk 'FNR==2 {print $1}'
```

### Worker node join command

* Token 생성 및 Join 문자열 출력

```
▒ kubeadm token create --print-join-command
```

*  Token 생성 및 Join 문자열 (수동)

```
▒ END_POINT=$(echo "$(kubeadm config view | grep controlPlaneEndpoint)" | awk '{ print $2}')
▒ TOKEN=$(kubeadm token create)
▒ HASH=$(openssl x509 -pubkey -in /etc/kubernetes/pki/ca.crt | openssl rsa -pubin -outform der 2>/dev/null | openssl dgst -sha256 -hex | sed 's/^.* //')

▒ echo "kubeadm join ${END_POINT} --token ${TOKEN} --discovery-token-ca-cert-hash sha256:${HASH}"
```


### Certificate-key
> certificate-key 발생시키고 kubeadm init 또는 join 시 사용

```
▒ CERT_KEY=$(kubeadm alpha certs certificate-key)

# master 초기화
▒ kubeadm init --certificate-key $CERT_KEY

# master (control plane) join
▒ kubeadm join <master-ip>:6443 \
--token <token> \
--discovery-token-ca-cert-hash sha256:<token-ca-cert> \
--control-plane \
--certificate-key $CERT_KEY
```

* certificate-key 업로드

```
▒ kubeadm init phase upload-certs --upload-certs
```

* 업로드된 certificate-key 는 2시간 동안 유지 후 삭제된다.


## etcdctl

```
# etcd 에서 노드 조회
▒ etcdctl member list

# etcd 에서 노드 제
▒ etcdctl member remove <id>
```
