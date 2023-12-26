1. 개요 : 회사의 형상관리는 SVN이다... 이를 GITLAB으로 마이그레이션 수행하는 임무가 떨어졌다...
2. 필요 Dependencies : git-svn. 설치 방법은 나보다 구글 신이 잘 안다.(아니면 ChatGPT 신...)
3. 과정
```
3.1) 적절한 장소에 새로운 디렉토리를 생성하고 그 곳으로 이동
> mkdir my_git_repo && cd my_git_repo

3.2) 그런 다음 다음 명령을 실행하여 SVN 저장소를 클론 수행. http://my_svn_repo는 실제 SVN 저장소의 URL로 대체 할 것. GITLAB으로 마이그레이션 수행 할, SVN의 원본을 클론하는 과정이다. (SVN 접근 권한은 잇을거라 생각합니다^^...)
> git svn clone http://my_svn_repo

3.3) GITLAB에다 원격 리포지토리를 생성할 것. GITLAB의 아주 친절한 WEB Interface를 통해 클론할 레포를 생성하자.

3.4) PUSH 수행 할 디렉토리로 이동합시다. 원격에다 PUSH 할거니까요.
> cd my_git_repo

3.5) 원격 레포와 연결해줍니다.
> git remote add origin https://my_gitlab_repo
* 참고 : 원격 레포와 연결 후, git init을 해줘야 하는 경우도 있습니다. 하지만, 형상관리를 해보신 분이라면 따로 설명은 안해도 되겠죠?

3.6) 푸시를 수행해줍니다.
> git push -u origin master

3.7) 마이그레이션 성공!!
```
