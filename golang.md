# golang

## go module command

* `go mod init` : go.mod 파일을 생성한다.
* `go list -m all` : Build시 사용된 Module들의 Version 확인한다.
* `go list -u -m all` : Module이 Patch 가능한지 확인한다.
* `go get -u` : Module을 Patch (Update)한다.
* `go mod tidy` : go.mod에서 불필요한 Module은 제거하고, 필요한 Module을 추가한다.
* `go mod vendor` : go.mod의 Module을 이용하여 vendor Directory를 생성한다.
* `go clean –modcache` : Module Cache ($GOPATH/pkg/mod)를 삭제한다.


## Libraries

### Kubernetes go-client

* 참고 : [Installing client-go](https://github.com/kubernetes/client-go/blob/master/INSTALL.md#enabling-go-modules)

```
▒ go mod init
▒ go get k8s.io/client-go@master
```
