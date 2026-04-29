---
title: Docker 입문 — 왜 쓰는가
date: 2019-12-16
category: devops
tags: [docker, container, devops]
summary: 애플리케이션 실행 환경을 표준화해서 환경에 구애받지 않고 배포·확장할 수 있게 하는 플랫폼. "Debug your app, not your environment."
---

## Docker란?

애플리케이션을 신속하게 구축·테스트·배포할 수 있는 소프트웨어 플랫폼.

## 왜 쓰는가

- 애플리케이션이 실행되는 환경을 표준화해서 관리한다.
- 환경에 구애받지 않고 애플리케이션을 배포·확장할 수 있다.

> ✨ **Debug your app, not your environment** ✨

## 맛보기

```bash
brew install --cask docker
docker run <image>
```

## 다음에 써먹을 곳

- 로컬에 의존 서비스(DB, ES, Redis) 띄울 때 시스템 오염 없이 컨테이너로.
- CI/CD에서 빌드·테스트 환경을 이미지로 고정해 재현성 확보.
