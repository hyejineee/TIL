---
title: Serverless 프레임워크와 AWS Lambda 개념
date: 2019-12-20
category: devops
tags: [serverless, aws, lambda]
summary: 이벤트가 발생하면 Lambda 함수를 실행시키는 방식. 서버리스 프레임워크가 배포·이벤트 연결을 추상화해준다.
---

## 배운 것

- **서버리스 프레임워크** — 서버 관리 없이 함수 단위로 배포·운영하는 패러다임.
- **이벤트 → Lambda**: 이벤트(HTTP, S3 업로드, 큐 메시지 등)가 발생하면 해당 Lambda 함수가 실행된다.

서버를 상시 띄우지 않고, 트리거 시점에만 함수가 가동된다는 게 핵심.
