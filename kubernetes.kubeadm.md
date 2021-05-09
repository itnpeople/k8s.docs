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


## Use Case

### Simple 
* 192.168.77.71 : public ip
* 10.30.20.101 : eth0

```
▒ cat << EOF > kubeadm-config.yaml
apiVersion: kubeadm.k8s.io/v1beta2
kind: ClusterConfiguration
controlPlaneEndpoint: 192.168.77.71:6443
dns:
  type: CoreDNS
apiServer:
  extraArgs:
    advertise-address: 10.30.20.101
    authorization-mode: Node,RBAC
  certSANs:
  - 10.30.20.101
  - localhost
  - 127.0.0.1
etcd:
  local:
    dataDir: /var/lib/etcd
networking:
  dnsDomain: cluster.local
  podSubnet: 10.244.0.0/16
  serviceSubnet: 10.96.0.0/12
controllerManager: {}
scheduler: {}
EOF

▒ sudo kubeadm init --v=5 --upload-certs --config kubeadm-config.yaml
```

