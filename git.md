# Git

## Head 이동

```
# history back -1
▒ git checkout HEAD~1

# come back
▒ git checkout master
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

* master 브랜치의 HEAD를  develop 브랜치 HEAD로 이동하고 리모트레파지토리에 적용
```
▒ git checkout develop
▒ git branch -f master HEAD
▒ git push origin +master
```

## Tag 

* 태깅
```
▒ git tag v0.2.8              # 현재 커밋에 태그
▒ git tag v0.2.8 03c0beb080   # 커밋 지정 태그
```

* Push
```
▒ git push origin --tags      # 모든 태그 push
▒ git push origin v0.2.8      # 태그 지정 push
```

* 태그 삭제
```
▒ git tag -d v0.2.8           # 삭제
▒ git push origin :v0.2.8     # Push
```

## Merge

* master 로 머지

```
▒ git checkout master
▒ git merge <branch name>
▒ git push origin master
```

### rebase merge
> merge commit 없이 하나의 branch로 merge

* develop branch 에서 master branch 로 merge 하는 경우 예

```
▒ git checkout develop      # develop branch 로 HEAD 이동
▒ git rebase master         # rebase
▒ git checkout master       # master branch 로 HEAD 이동
▒ git merge develop         # megee 수행
▒ git push origin master    # master push
```

### Squashed branch merge
> 한 브랜치의 이력을 압축하여 다른 브랜치의 최신 커밋 하나로 merge

```
▒ git checkout master               # merge 하고자 하는 branch 로 HEAD 이동
▒ git merge --squash feature/archi  # mater branch 로 feature/archi branch 를 merge
▒ git commit -m 'Add ...'           # master branch 커밋
▒ git push origin master            # master push
```

## 취소 (reset)

* HEAD
  * <commit id> : 특정 HEAD 위치 지정 (돌아가고 싶은 위치)
  * HEAD@{숫자} : 이전 {숫자} 만큼 이전 위치
  * HEAD^ : 현재 바로 이전 위치로 이동

### Commit 취소

* Reset 옵션
  * --mixed (default): 변경 이력 삭제, 변경내역은 Unstage 상태
  * --soft : 변경 이력 삭제, Stage 상태는 유지
  * --hard : 돌아가려는 이력이후의 모든 내용을 삭제

```
▒ git reset <HEAD>            # default --mixed
▒ git reset <option> <HEAD>
```

* 참조
  * [Git 도구 - Reset 명확히 알고 가기](https://git-scm.com/book/ko/v2/Git-%EB%8F%84%EA%B5%AC-Reset-%EB%AA%85%ED%99%95%ED%9E%88-%EC%95%8C%EA%B3%A0-%EA%B0%80%EA%B8%B0)
  * [작업 되돌리는 명령어 Reset & Revert](https://velog.io/@ha0kim/GIT-%EC%9E%91%EC%97%85-%EB%90%98%EB%8F%8C%EB%A6%AC%EB%8A%94-%EB%AA%85%EB%A0%B9%EC%96%B4-Reset-Revert)


### Push 취소

* 되돌아간 commit 이후의 모든 commit 정보 모두 삭제
* local의 내용을 remote에 강제로 덮어쓰기

```
▒ git reset <HEAD>
▒ git push origin +<branch name>
▒ git pull
```

## 커밋

### 커밋 합치기(squash)

1. HEAD(현재 최신위치) 에서 변경하고 싶은 commit 으로 rebase
1. 합치고 싶은 커밋을 pick 을 `squash` 또는 `s` 로 변경 하고 저장 (:wq)
1. 커밋메시지를 수정하고 저장 (:wq) 
1. git 푸쉬 (--force)

~~~
▒ git rebase -i HEAD~4

pick 01d1124 Adding license
squash 6340aaa Moving license into its own file
squash ebfd367 Jekyll has become self-aware.
squash 30e0ccb Changed the tagline in the binary, too.

▒ git push origin +master
~~~

### 커밋 메시지 수정(reword)

1. HEAD(현재 최신위치) 에서 변경하고 싶은 commit 으로 rebase
1. 수정하고 싶은 싶은 커밋을  `pick` 에서 `reword` 또는 `r` 로 변경 하고 저장 (:wq)
1. 커밋메시지를 수정하고 저장 (:wq) 
1. git 푸쉬 (--force)

```
▒ git rebase -i HEAD~3

pick e499d89 Delete CNAME
reword 0c39034 Better README
pick f7fde4a Change the commit message but push the same commit.

▒ git push origin +master
```

* 최근 메시지 수정

```
▒ git commit --amend
```


## 리모트 저장소와의 동기화

### 브랜치 최신화

로컬 브랜치를 리모트 저장소의 브랜치와 동기화하고 최신화(최신 커밋을 참조)하려면 
master 브랜치를 먼저 패치한 후 최신화 하고자 하는 브랜치로 체크아웃하여 리셋을 실행한다.

```
# master 패치

▒ git checkout master
▒ git fetch
▒ git pull origin master

#  브랜치 리셋

▒ git checkout <branch name>
▒ git reset --hard origin/<branch name>
```

* `git fetch` 명령은 원격 저장소로 부터 가져온 모든 브랜치의 헤드를 .git/FETCH_HEAD 파일에 기록
* FETCH_HEAD는 원격 저장소로부터 가져온 브랜치의 HEAD를 의미
* `reset --hard` 명령은 작업 디렉토리와 인덱스의 상태를 모두 리셋하므로 주의


### 포크한 저장소와 원본 저장소 동기화

* 원본 저장소 (upstream) 등록
```
▒ git remote add upstream <remote repository url>
▒ git remote -v
```

* fetch 및 branch 별 동기화
```
▒ git fetch upstream

▒ git checkout master
▒ git rebase upstream/master
▒ git push origin +master

▒ git checkout develop
▒ git rebase upstream/develop
▒ git push origin +develop
```

* 테그가 동기화되어있지 않다면

```
▒ git fetch upstream v0.1.2
▒ git tag v0.1.2 FETCH_HEAD
▒ git push origin v0.1.2
```


## 삭제된 Branch 복구하기

* git reflog 확인
```
▒ git reflog
```

* HEAD@{숫자}로 이루어진 로그 중
* 돌아가고 싶은 상태의 숫자를 확인

```
▒ git checkout -b <삭제된 브랜치 이름> HEAD@{숫자}
```


## Git 그래프 보기
```
▒ git log --graph  --abbrev-commit --pretty=oneline --all
```

## Subtree

### `subtree` 구성

```
# dashboard 디렉토리에 repository branch 로 subtree 구성
▒ git subtree add --squash --prefix=<sub dirctory> <https://github.com/xxxx/xxxxx.git> <branch name>
```

* subtree 대상 repository 의 커밋 history도 유지(default) 
* 커밋 history를 하나로 합쳐 구성하고자 하면 `--squash` 옵션 사용


* `subtree`로 구성된 소스 중 사용되지 않는 파일을 제외하도록 지정하고 소스 트리를 업데이트

```
# sparse checkout 옵션 지정
▒ git config core.sparsecheckout true

# 대상 파일 지정
▒ cat <<EOF> .git/info/sparse-checkout
/*
!/dashboard
/dashboard/Dockerfile
/dashboard/gulpfile.babel.js
!/src/app/metrics-scraper
/src/app/metrics-scraper/hack/build.sh
/src/app/metrics-scraper/pkg
!/src/app/metrics-scraper/**/*_test.go
EOF

# 트리 업데이트
▒ git read-tree HEAD -m -u
```


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

* 커밋을 force Push (Push된 내용은 Pull Request에 자동 자동 반영)
```
▒ git push origin <branch name> --force 
```


## Pull Request Local 에 다운로드 받기

* `.git.config` 파일을 열어  romete 섹션에 `fetch = +refs/pull/*/head:refs/remotes/origin/pr/*` 을 추가 (remote 가 `upstream` 인 경우)

```
▒ vi .git.config
```

```
[remote "upstream"]
  url = git@github.com:<username>/<project-name>.git
  fetch = +refs/heads/*:refs/remotes/origin/*
  fetch = +refs/pull/*/head:refs/remotes/origin/pr/*
```

```
▒ git fetch upstream
▒ git checkout origin/pr/17
```

## Git-Flow (브랜치 전략)

* [Basic Git Flow For Making Open Source Contributions on GitHub](https://dnncommunity.org/blogs/Post/1470/Basic-Git-Flow-For-Making-Open-Source-Contributions-on-GitHub)
* [우린 Git-flow를 사용하고 있어요](https://woowabros.github.io/experience/2017/10/30/baemin-mobile-git-branch-strategy.html)
* [브랜치의 종류](https://mylko72.gitbooks.io/git/content/branch/branch_type.html)

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


## Github Container Registry

```
ghcr.io/<GITHUB_ID>/<IMAGE_NAME>:<TAG>
```

### 시작하기
> [깃허브 컨테이너 레지스트리 베타 오픈 및 사용법](https://www.44bits.io/ko/post/news--github-container-registry-beta-release#%EA%B9%83%ED%97%88%EB%B8%8C-%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88-%EB%A0%88%EC%A7%80%EC%8A%A4%ED%8A%B8%EB%A6%ACgithub-container-registry-%EC%8B%9C%EC%9E%91%ED%95%98%EA%B8%B0)

1. Github 프로필 페이지에서 "Settings > Developer settings > Personal access tokens" 선택
1. "Generate New Toekn" 버튼 클릭
1. 생성될 Token의 권한 선택 ("repo", "write:packages", "delete:packages", "workflow" 등)
1. "Generate token" 버튼 클릭
1. 생성된 토큰을 복사하여 로컬파일로 저장 (예: github.itnpeople.token)
1. [Enabling improved container support](https://docs.github.com/en/free-pro-team@latest/packages/getting-started-with-github-container-registry/enabling-improved-container-support) : "우측상단 계정 아이콘 > Feature Preview 선택 > mproved container support" 에서 "Enable" 버튼 클릭

* Registry 로그인

```
▒ cat github.itnpeople.token | docker login ghcr.io -u itnpeople --password-stdin
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

  