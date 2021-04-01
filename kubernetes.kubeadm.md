# kubeadm


## Token

* 현재 token 조회

```
▒ kubeadm token list 
```

### Worker join command 

* Token 재사용 - `system:bootstrappers:kubeadm:default-node-token` 토큰이 expires 되지 않았다면

```
▒ TOKEN="$(sudo kubeadm token list | awk 'FNR==2 {print $1}')"
▒ HASH="$(openssl x509 -pubkey -in /etc/kubernetes/pki/ca.crt | openssl rsa -pubin -outform der 2>/dev/null | openssl dgst -sha256 -hex | sed 's/^.* //')"
▒ END_POINT=$(echo "$(sudo kubeadm config view | grep controlPlaneEndpoint)" | awk '{ print $2}')

▒ echo "sudo kubeadm join ${END_POINT} --token ${TOKEN} --discovery-token-ca-cert-hash sha256:${HASH}"
```

*  Token 신규 생성

```
▒ kubeadm token create --print-join-command
```

### Control-plane join command 

```
# certificate-key generate
▒ CERT_KEY=$(sudo kubeadm alpha certs certificate-key)

▒ TOKEN="$(sudo kubeadm token list | awk 'FNR==2 {print $1}')"
▒ HASH="$(openssl x509 -pubkey -in /etc/kubernetes/pki/ca.crt | openssl rsa -pubin -outform der 2>/dev/null | openssl dgst -sha256 -hex | sed 's/^.* //')"
▒ END_POINT=$(echo "$(sudo kubeadm config view | grep controlPlaneEndpoint)" | awk '{ print $2}')

▒ echo "sudo kubeadm join ${END_POINT} --token ${TOKEN} --discovery-token-ca-cert-hash sha256:${HASH} --control-plane --certificate-key ${CERT_KEY}"
```


* Token & cert-key 신규 생성

```
# certificate-key 업로드 
# - 업로드된 certificate-key 는 2시간 동안 유지 후 삭제
▒ kubeadm init phase upload-certs --upload-certs

CERT_KEY="<출력된 cert-key>"
▒ sudo kubeadm token create --print-join-command --certificate-key ${CERT_KEY}
```



## etcdctl

```
# etcd 에서 노드 조회
▒ etcdctl member list

# etcd 에서 노드 제
▒ etcdctl member remove <id>
```
