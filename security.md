# Security


## ssl (self-sign) 적용

```
▒ KEY_FILE=server.key
▒ CERT_FILE=server.crt
▒ CERT_NAME=docker-registry-tls-cert
▒ IP=101.55.69.105
▒ HOST=$IP:30500

# 인증서 생성
▒ openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout ${KEY_FILE} -out ${CERT_FILE} -subj "/CN=${IP}/O=${IP}" -reqexts SAN -extensions SAN -config <(cat /etc/ssl/openssl.cnf <(printf "[SAN]\nsubjectAltName=IP:${IP}"))

# 인증서로 TLS secret 생성
▒ kubectl create secret tls ${CERT_NAME} --key ${KEY_FILE} --cert ${CERT_FILE}

# Mac 키체인에  시스템 인증서로 등록 (mac) (https://docs.docker.com/docker-for-mac/#add-custom-ca-certificates-server-side)
▒ sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain ${CERT_FILE}
```