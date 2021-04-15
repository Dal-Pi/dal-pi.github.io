---
title: "[git] 하나의 파일만 hard reset하기"
date: 2021-04-16 00:30:00 +0900
categories:
  - git
tags:
  - git
---
Method to reset only some files.

수정된 여러 파일들이 있는 경우 하나 혹은 몇 개만 되돌리고 싶을 때가 있다. 이런 경우에는
```
$ git checkout -- <파일>
```
을 사용하면 된다. 

-----
예를 들면
```
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)
        ^-- 사실 git status를 치는 순간 알려주고 있다!

        modified:   A
        modified:   B

no changes added to commit (use "git add" and/or "git commit -a")
```
와 같을 때 보통 현재 HEAD로 파일을 되돌릴 경우 hard reset을 사용하는데  
hard reset은 모든 파일만 되돌릴 수 있으며 별개의 파일 목록에 대한 reset을 제공하지 않는다.  
```
$ git reset --hard A
fatal: Cannot do hard reset with paths.
```
이런 경우에는 checkout키워드를 사용하여  
```
$ git checkout -- A

$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   B

no changes added to commit (use "git add" and/or "git commit -a")
```
와 같이 특정 파일만 reset할 수 있다.  
   
이 때 checkout 뒤의 `--`의 의미는 branch 이름과 file 이름을 구분하기 위함이다  
정확히 하면 `<tree-ish>` 와 `<pathsepc>`으로 표현하는데 자세한 설명은 [git-checkout 문서](https://git-scm.com/docs/git-checkout)를 참조.  

이것도 간단한 예를 들면  
`master` 라는 파일을 `master` 브렌치에서 수정하고 있는 경우에(이렇게 사용하는 사람이 있을까 싶지만..)  
```
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   master

no changes added to commit (use "git add" and/or "git commit -a")

$ git checkout master
Already on 'master'
M       master
```
`git checkout master`를 하면 master branch로 체크아웃 하려 하는 것으로 이해한다.  
`--`를 쓴 뒤의 master는 파일 이름으로 해석한다. 이 또한 자세한 설명은 [git-checkout 문서](https://git-scm.com/docs/git-checkout)를 참조.  
```
$ git checkout -- master

$ git status
On branch master
nothing to commit, working tree clean
```
branch 명을 생략하면 현재 HEAD를 의미하므로 `git checkout master -- master`를 의미한다.  
