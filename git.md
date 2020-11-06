# Git

## 커밋 합치기(squash)
 
~~~
# HEAD 에서 이전 4개 commit 으로 rebase
▒ git rebase -i HEAD~4

# 2~4번 커밋을 1번 커밋에 squash 하고 저장

pick 01d1124 Adding license
squash 6340aaa Moving license into its own file
squash ebfd367 Jekyll has become self-aware.
squash 30e0ccb Changed the tagline in the binary, too.

# push 
▒ git push origin +master
~~~


## Pull Request

### 개발 준비작업
> 개발 작업 이전에 forked repoistory 에서 clone 받은 디렉토리에  원격 리모트(upstream)가 없다면 원격 리모트를 추가하고 forked repository 에 원격 repository 의 최신 소스들을 동기화 한다.
 

* 원격 리모트 추가
```
▒ git remote add upstream <remote repository url>
▒ git remote -v
```

* 리모트 최신소스를 forked repositry master branch에 동기화 (rebase)
```
▒ git checkout master
▒ git fetch upstream
▒ git rebase upstream/master
```

* 충돌이 발생할 경우 해당 파일을 수정
  * 충돌 메뉴얼로 수정 후 commit 단계별 `rebase --continue` 반복 작업
  * 최신 동기화 이전에 branch 의 커밋을 합치면 보다 수월한 작업 (squash) 가능

```
▒ git add *
▒ git rebase --continue
```

*  문제 없이 rebase가 완료되면 origin master branch에 Push
```
▒ git push origin +master
```

### 개발
> 개발 할 branch를 신규 생성하고 해당 branch 에서 개발 작업을 수행한다.


* 새로 개발 할 branch  만들기 

```
▒ git branch <branchn ame>
▒ git push origin <branch name>
▒ git checkout <branch name>
```

* 개발수행 


### Pull Request 준비 작업
> "Pull Request" 수행 전 커밋을 합친 후 다시 한번 원격 리모트 repository 와 동기화하여 최신 소스를 branch 에 반영한다.


* 보다 수월한 upstream 최신 동기화(rebase)를 위해 커밋 합치기 (squash)

```
▒ git rebase -i HEAD~4
# .. 이후 작업은 <커밋 합치기(squash)> 참조
```

* 최신으로 동기화 (rebase)
 
```
▒ git checkout <branch name>
▒ git fetch upstream
▒ git rebase upstream/master
```

* Fork 받은 http://github.com/<user>/<repository name> 에서 "Pull Request" 생성

### 리뷰 대응

* 리뷰 내용 수정 반영

* 스태이징(add) & 커밋(commit)

```
▒ git commit –a –m “Rev 1 xxxxxx”                  
```

* 수정된 소스를 "Pull Request"에 업데이트 (Forked repository branch 에서 --force 옵션을 주고 Push)
```
▒ git push origin <branch name> --force 
```

## Branch


* branch 이동

```
▒ git checkout <branchname>
```

* branch  만들기
```
▒ git branch <branchname>
▒ git push origin <branchname>
▒ git checkout <branchname>
```

* branch 삭제

```
▒ git checkout master
▒ git branch -D <branchname>
▒ git push origin :<branchname>
```


## Merge

* master 로 머지

```
▒ git checkout master
▒ git merge <branch name>
▒ git commit -m 'Update contact page'
▒ git push origin master
```

### Squashed branch merge
> 한 브랜치의 이력을 압축하여 다른 브랜치의 최신 커밋 하나로 머지

```
▒ git checkout master               # 머지하고자 하는 branch 로 HEAD 이동
▒ git merge --squash feature/archi  # mater branch 로 feature/archi branch 를 merge
▒ git commit -m 'Add ...'           # master branch 커밋
```

## 커밋

* 이전 커밋로그 수정
```
▒ git commit --amend
▒ git push origin +master
```

* 커밋 취소 (push 이전)
```
▒ git reset HEAD~1
```


## Git 그래프 보기
```
▒ git log --graph  --abbrev-commit --pretty=oneline --all
```


## GitHub SSH 다중 계정 관리 (macOS)
>git에서 게정별로 구분하는 방법이 없어 `ssh-agent` 를 활용을 통해 가능


* 새로운 키 생성
```
▒ ssh-keygen -t rsa -f ~/.ssh/acornsoftlab-key -C "escho@acornsoft.io"
```

* 키 복사
```
▒ cat ~/.ssh/acornsoftlab-key.pub 
```

* Github SSH 키 등록
  * 공식문서  참조 : [Adding a new SSH key to your GitHub account](https://help.github.com/en/github/authenticating-to-github/adding-a-new-ssh-key-to-your-github-account)

* 해당 SSH 키로 github SSH 연결 확인

```
▒ ssh -T -i ~/.ssh/acornsoftlab-key git@github.com

Hi acornsoftlab! You've successfully authenticated, but GitHub does not provide shell access.
```

* **ssh-agnet** 실행여부 확인 

```
▒ eval "$(ssh-agent -s)"

Agent pid 8086
```

* **ssh-agnet** 에 키 등록 & 확인

```
▒ ssh-add ~/.ssh/acornsoftlab-key
▒ ssh-add -l
```

* **ssh-agnet** config 파일 설정
  * `github.aconsoftlab` : github 계정을 구별하기 위한 표시
  * `HostName` : 실제 도메인

```
▒ vi ~/.ssh/config
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
▒ ssh-add -D 
▒ ssh-add ~/.ssh/acornsoftlab-key
▒ ssh-add -l
```

* git clone

```
▒ git clone git@github.com:account_name/repo_name.git
▒ cd repo_name
```

* remote 주소 변경 
  * `github.com`을 이전 `~/.ssh/config`에 지정했던  계정을 구별 표시(`github.acornsoftlab`) 변경
```
▒ git remote add origin git@github.acornsoftlab:woaccount_name/repo_name.git 
```

* push 테스트

```
▒ git add .
▒ git commit -m "Initial commit"
▒ git push -u origin master
```

### 참고
* [How to manage multiple GitHub accounts on a single machine with SSH keys](https://www.freecodecamp.org/news/manage-multiple-github-accounts-the-ssh-way-2dadc30ccaca/)
* [Github 다수 계정을 위한 SSH Key 설정](https://mygumi.tistory.com/96)
* [Mac 에서 ssh-agent 사용하기](https://blog.munilive.com/using-ssh-agent-on-mac-os/)
