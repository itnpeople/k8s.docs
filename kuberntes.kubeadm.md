# kubeadm


## kubeadm

### token

```
# --token
▒  kubeadm token list | awk 'FNR==2 {print $1}')

# --discovery-token-ca-cert-hash
▒  openssl x509 -pubkey -in /etc/kubernetes/pki/ca.crt | openssl rsa -pubin -outform der 2>/dev/null | openssl dgst -sha256 -hex | sed 's/^.* //')
```

### certificate-key

certificate-key 발생시키고 kubeadm init 또는 control plane join 시 사용할 수 있음 

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

certificate-key 업로드

```
▒ kubeadm init phase upload-certs --upload-certs
```

업로드된 certificate-key 는 2시간 동안 유지 후 삭제


## etctctl

```
# etcd 에서 노드 조회
▒ etcdctl member list

# etcd 에서 노드 제
▒ etcdctl member remove <id>
```

## use-case

### 비 정상 master 노드를 reset 시키고 클러스터에 control plane 으로 다시 join 시키는 방법
//TODO

정상 마스터 노드 접속

etcd 에서 멤버여부 확인하고 멤버에서 제거

```
# 조회
▒ etcdctl member list

# 삭제 (필요시)
▒ etcdctl member remove <id>
```

업로드

```
▒ sudo kubeadm init phase upload-certs --upload-certs
```