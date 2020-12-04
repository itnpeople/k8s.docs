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

### 1단계. 준비작업
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

### 2단계. 개발
> 개발 할 branch를 신규 생성하고 해당 branch 에서 개발 작업을 수행한다.


* 새로 개발 할 branch  만들기 

```
▒ git branch <branchn ame>
▒ git push origin <branch name>
▒ git checkout <branch name>
```

* 개발작업을 수행 한다.


### 3단계. Pull Request 준비 작업
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

### 4단계. Pull Request

* Fork 받은 http://github.com/<user>/<repository name> 에서 "Pull Request" 생성

### 5단계. 리뷰 대응

* 리뷰 내용 수정 반영

* 스태이징(add) & 커밋(commit)

```
▒ git commit –a –m “Rev 1 xxxxxx”                  
```

* 수정된 소스를 "Pull Request"에 업데이트 (Forked repository branch 에서 --force 옵션을 주고 Push)
```
▒ git push origin <branch name> --force 
```

## Pull Request Local 에 다운로드 받기

* `.git.config` 파일을 열어  romete 섹션에 `fetch = +refs/pull/*/head:refs/remotes/origin/pr/*` 을 추가
* 만일 remote 섹션 `upstream` 일 경우

```
$ vi .git.config
```

```
[remote "upstream"]
  url = git@github.com:<username>/<project-name>.git
  fetch = +refs/heads/*:refs/remotes/origin/*
  fetch = +refs/pull/*/head:refs/remotes/origin/pr/*
```

```
$ git fetch upstream
$ git checkout origin/pr/17
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


## 복구하기 (reflog)

### 실수로 삭제한 branch 복구

* git reflog 확인
```
$ git reflog
```

* HEAD@{숫자}로 이루어진 로그 중
* 돌아가고 싶은 상태의 숫자를 확인하고 아래의 명령어에 입력
```
$ git checkout -b <삭제된 브랜치 이름> HEAD@{숫자}
```

### 실수로 삭제한 Commit 복구하기

* git reflog 확인
```
$ git reflog
```

* commit id를 이용, 코드 복구

```
$ git reset --hard commit_id
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

## Tag 

* 태깅
```
$ git tag v0.2.8              # 현재 커밋에 태그
$ git tag v0.2.8 03c0beb080   # 커밋 지정 태그
```

* Push
```
$ git push origin --tags      # 모든 태그 push
$ git push origin v0.2.8      # 태그 지정 push
```

* 태그 삭제
```
$ git tag -d v0.2.8           # 삭제
$ git push origin :v0.2.8     # Push
```

## Github Container Registry

```
ghcr.io/<GITHUB_ID>/<IMAGE_NAME>:<TAG>
```

### 시작하기
> > [깃허브 컨테이너 레지스트리 베타 오픈 및 사용법](https://www.44bits.io/ko/post/news--github-container-registry-beta-release#%EA%B9%83%ED%97%88%EB%B8%8C-%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88-%EB%A0%88%EC%A7%80%EC%8A%A4%ED%8A%B8%EB%A6%ACgithub-container-registry-%EC%8B%9C%EC%9E%91%ED%95%98%EA%B8%B0)

1. Github 프로필 페이지에서 "Settings > Developer settings > Personal access tokens" 선택
1. "Generate New Toekn" 버튼 클릭
1. 생성될 Token의 권한 선택 ("repo", "write:packages", "delete:packages", "workflow" 등)
1. "Generate token" 버튼 클릭
1. 생성된 토큰을 복사하여 로컬파일로 저장 (예: github.itnpeople.token)
1. [Enabling improved container support](https://docs.github.com/en/free-pro-team@latest/packages/getting-started-with-github-container-registry/enabling-improved-container-support) : "우측상단 계정 아이콘 > Feature Preview 선택 > mproved container support" 에서 "Enable" 버튼 클릭

* Registry 로그인

```
$ cat github.itnpeople.token | docker login ghcr.io -u itnpeople --password-stdin
```

* Github 프로필 페이지 "Packages" 탭에서 조회


## Github Workflow

### Github action trigger workflow

* workflow 실행은 Repository 이벤트 발생시 실행되지만 원하는 workflow yaml 파일에 `workflow_dispatch`를 선언해주면 "Repository > Actions" 화면에서 Workflow 선택 시  "Run workflow" 버튼이 활성화되어 메뉴얼로 Workflow 를 실행할 수 있다.

```
on: 
  workflow_dispatch:
    inputs:
      logLevel:
        description: 'Log level'     
        required: true
        default: 'warning'
      tags:
        description: 'Test scenario tags'  
        required: true
```

* logLevel, tags 는 변수명으로 `${{ github.event.inputs.logLevel }}` 와 같이 환경변수로 활용, 아래 예제 참조

```
jobs:
  printInputs:
    runs-on: ubuntu-latest
    steps:
    - run: |
        echo "Log level: ${{ github.event.inputs.logLevel }}"
        echo "Tags: ${{ github.event.inputs.tags }}" 
```

### `docker/build-push-action@v2` 활용 시 서브 디렉토리에 있는 Dockerfile 을 빌드

아래와 같이 step.with 에 `context`, `file` 값을 지정해준다.

```
jobs:
  push_to_registries:
    name: Push Docker image to multiple registries
    runs-on: ubuntu-latest
    steps:
      - name: Build & Push to Docker Hub
        uses: docker/build-push-action@v2
        with:
          context: src/app/backend
          tags: acornsoftlab/acornsoft.backend:${{steps.variables.outputs.version}}
          file: src/app/backend/Dockerfile
          push: true
```

### (예제) Variable 및  버전 조회 

* 변수값을 지정할 때는 `echo ::set-output name=version::${VERSION}` 로 output 처리하고 
* 변수값을 사용하는 쪽에서는  `${{steps.variables.outputs.version}}` 와 같이 조회한다.

```
jobs:
  push_to_registries:
    name: Push Docker image to multiple registries
    runs-on: ubuntu-latest
    steps:
      - name: Set variables
        id: variables
        run: |
          VERSION="${{ github.event.inputs.version }}"
          if [[ $VERSION == "" ]]; then
            if [[ $GITHUB_REF == refs/tags/* ]]; then
              VERSION=${GITHUB_REF#refs/tags/}
            elif [[ $GITHUB_REF == refs/heads/* ]]; then
              VERSION=$(echo ${GITHUB_REF#refs/heads/} | sed -r 's#/+#-#g')
            elif [[ $GITHUB_REF == refs/pull/* ]]; then
              VERSION=pr-${{ github.event.number }}
            fi
          fi
          echo ::set-output name=version::${VERSION}
```


## Github Badges

![Version](https://img.shields.io/github/release/cloud-barista/cb-ladybug)
```
![Version](https://img.shields.io/github/release/:user/:repository)
```

![License](https://img.shields.io/github/license/cloud-barista/cb-ladybug)
```
![License](https://img.shields.io/github/license/:user/:repository)
```

![issues](https://img.shields.io/github/issues/cloud-barista/cb-ladybug)
```
![issues](https://img.shields.io/github/issues/:user/:repository)
```

![issues](https://img.shields.io/github/issues-closed/cloud-barista/cb-ladybug)
```
![issues](https://img.shields.io/github/issues-closed/:user/:repository)
```

![pull requests](https://img.shields.io/github/issues-pr-closed/cloud-barista/cb-ladybug)
```
![pull requests](https://img.shields.io/github/issues-pr-closed/:user/:repository)
```

![workflow](https://img.shields.io/github/workflow/status/cloud-barista/cb-ladybug/Docker)
```
![workflow](https://img.shields.io/github/workflow/status/:user/:repository/Docker)
```

![code size](https://img.shields.io/github/languages/code-size/:user/:repository)
```
![code size](https://img.shields.io/github/languages/code-size/:user/:repository)
```

![follow](https://img.shields.io/github/followers/cloud-barista?style=social)
```
![follow](https://img.shields.io/github/followers/:user?style=social)
```

![fork](https://img.shields.io/github/forks/cloud-barista/cb-ladybug?style=social)
```
![fork](https://img.shields.io/github/forks/:user/:repository?style=social)
```