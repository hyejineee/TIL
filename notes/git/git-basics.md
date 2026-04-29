---
title: Git 기본 명령어 — amend, force push, rebase
date: 2019-12-11
category: git
tags: [git, vcs]
summary: commit --amend로 이전 커밋 수정, push -f로 PR 덮어쓰기, fetch 후 master에서 rebase 하는 기본 흐름.
---

## 배운 것

### git commit --amend
이전에 커밋한 것을 수정할 수 있다.

### git push origin <branch> -f
이전 PR을 덮어쓸 수 있다. 리뷰 반영 시 새 커밋을 쌓지 않고 같은 커밋을 갈아끼우고 싶을 때 유용.

### fetch + rebase
`git fetch` 한 뒤 master 브랜치에서 rebase. 리모트 최신 상태를 받아온 다음 로컬 브랜치를 그 위에 올린다.

## 다음에 써먹을 곳

- PR 리뷰 반영할 때 새 커밋 쌓지 말고 amend → force push로 깔끔하게 유지.
- master가 바뀌었을 때 merge 대신 rebase로 히스토리 선형 유지.
