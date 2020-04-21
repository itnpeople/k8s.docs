# Git

## GitHub SSH 다중 계정 관리

* 새로운 키 생성
```
ssh-keygen -t rsa -f ~/.ssh/acornsoftlab-key -C "escho@acornsoft.io"
```

* 키 복사
```
cat ~/.ssh/acornsoftlab-key.pub 
```

* Github SSH 키 등록
  * 공식문서  참조 : [Adding a new SSH key to your GitHub account](https://help.github.com/en/github/authenticating-to-github/adding-a-new-ssh-key-to-your-github-account)

* 해당 SSH 키로 github SSH 연결 확인

```
ssh -T -i ~/.ssh/acornsoftlab-key git@github.com

Hi acornsoftlab! You've successfully authenticated, but GitHub does not provide shell access.
```

* **ssh-agnet** 실행여부 확인 

```
eval "$(ssh-agent -s)"

Agent pid 8086
```

* **ssh-agnet** 에 키 등록 & 확인

```
ssh-add ~/.ssh/acornsoftlab-key
ssh-add -l
```

* **ssh-agnet** config 파일 설정
  * `github.aconsoftlab` : github 계정을 구별하기 위한 표시
  * `HostName` : 실제 도메인

```
vi ~/.ssh/config
```

```
Host github.com
   HostName github.com
   User git
   IdentityFile ~/.ssh/id_rsa
Host github.aconsoftlab
  HostName github.com
  User git
  IdentityFile ~/.ssh/acornsoftlab-key
```

* **ssh-agnet** 에서 앤트리 삭제 후 재 등록 및  확인

```
ssh-add -D 
ssh-add ~/.ssh/acornsoftlab-key
ssh-add -l
```

* git clone

```
git clone git@github.com:account_name/repo_name.git
cd repo_name
```

* remote 주소 변경 
  * `github.com`을 이전 `~/.ssh/config`에 지정했던  계정을 구별 표시(`github.acornsoftlab`) 변경
```
git remote add origin git@github.acornsoftlab:woaccount_name/repo_name.git 
```

* push 테스트

```
git add .
git commit -m "Initial commit"
git push -u origin master
```

### 참고
* [How to manage multiple GitHub accounts on a single machine with SSH keys](https://www.freecodecamp.org/news/manage-multiple-github-accounts-the-ssh-way-2dadc30ccaca/)
* [Mac 에서 ssh-agent 사용하기](https://blog.munilive.com/using-ssh-agent-on-mac-os/)