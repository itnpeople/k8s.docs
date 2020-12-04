# Troubleshooting

## DNS

```
$ kubectl apply -f  - <<EOF
apiVersion: v1
kind: Pod
metadata:
  name: dnsutils
  namespace: default
spec:
  containers:
  - name: dnsutils
    image: gcr.io/kubernetes-e2e-test-images/dnsutils:1.3
    command:
      - sleep
      - "3600"
    imagePullPolicy: IfNotPresent
  restartPolicy: Always
EOF
```

```
$ kubectl exec dnsutils -- nslookup kubernetes.default
```

## Nginx

* 외부 통신 확인

```
$ kubeclt run nginx --image=nginx --restart=Never
$ kubeclt exec nginx -- curl -s http://www.naver.com
```

* 내부 통신 확인
```
$ kubeclt run nginx-a --image=nginx --replicas=2
$ kubectl expose deploy nginx-a --type=ClusterIP --name=nginx-a --port 80
```